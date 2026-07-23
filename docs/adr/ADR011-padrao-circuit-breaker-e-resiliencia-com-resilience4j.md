# ADR-011: Padrão Circuit Breaker e Resiliência com Resilience4j

* **Status:** Proposto
* **Data:** 2026-07-22

## Contexto

A API Farol da Fé depende de um provedor externo de IA Generativa (Google Gemini - ADR-006). APIs externas estão sujeitas a períodos de degradação, instabilidade de rede, erros HTTP 5xx temporários ou falhas de infraestrutura do próprio provedor.

Sem um mecanismo adequado de proteção contra falhas em cascata, se o provedor de IA ficar indisponível, a aplicação continuará tentando realizar chamadas HTTP bloqueantes até atingir o limite de timeout (30 segundos), acumulando requisições presas, esgotando Virtual Threads e degradando a experiência de todos os usuários.

## Decisão

Adotaremos a implementação do padrão **Circuit Breaker (Disjuntor de Falhas)** através da biblioteca **Resilience4j** na comunicação com o adaptador de IA (`GeminiAiAdapter`).

### 1. Estados do Circuit Breaker

```
[ FECHADO (Normal) ] ──(Falhas > 50%)──> [ ABERTO (Falha Total) ]
         ▲                                      │
         │                                (Após 30s)
         │                                      ▼
   (Sucesso 100%) ◄─────── [ MEIO-ABERTO (Testando) ]
```

* **Estado Fechado (Closed - Operação Normal):** Requisições fluem normalmente para a API do Gemini.
* **Estado Aberto (Open - Disjuntor Desarmado):** Se a taxa de falhas (erros 5xx ou timeouts) ultrapassar **50%** em uma janela de 10 requisições, o disjuntor abre por **30 segundos**. Durante esse período, nenhuma chamada é enviada ao Gemini; a aplicação retorna imediatamente a exceção tratada `HTTP 503 Service Unavailable`.
* **Estado Meio-Aberto (Half-Open - Teste de Recuperação):** Após 30 segundos, o disjuntor permite que poucas requisições de teste passem. Se forem bem-sucedidas, o disjuntor fecha novamente; se falharem, volta a abrir.

### 2. Estratégia de Fallback
Quando o disjuntor estiver no estado **Aberto**, a aplicação intercepta a chamada no `GeminiAiAdapter` e executa um método de *Fallback*, retornando uma mensagem amigável predefinida sem consumir recursos de rede.

## Consequências

### Positivas
* **Proteção contra Falhas em Cascata:** Evita que falhas na API do Gemini paralisem a aplicação inteira.
* **Respostas Rápidas em Cenários de Erro:** Em vez de aguardar 30s de timeout durante uma queda do Gemini, o cliente recebe uma resposta HTTP 503 instantânea.
* **Recuperação Automática:** O sistema testa e se restabelece sozinho assim que o provedor externo volta a operar.

### Negativas
* Adiciona complexidade na configuração de parâmetros do Resilience4j (janela de amostragem, limites de falha e tempos de espera).
