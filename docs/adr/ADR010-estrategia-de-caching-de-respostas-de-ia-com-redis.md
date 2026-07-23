# ADR-010: Estratégia de Caching de Respostas de IA com Redis

* **Status:** Proposto
* **Data:** 2026-07-22

## Contexto

A geração de análises exegéticas via API de IA Generativa (Google Gemini - ADR-006) apresenta latência média entre 2 e 6 segundos e consumo de cota de tokens por requisição.

Em cenários reais de uso, pesquisas por passagens bíblicas populares ou temas frequentes (ex: "Filipenses 4:13", "Salmo 23", "João 3:16") tendem a se repetir com alta frequência entre diferentes usuários. Reexecutar a chamada à IA para temas idênticos gera custos desnecessários de tokens e latência evitável para o usuário final.

## Decisão

Adotaremos a implementação de uma camada de **Cache de Respostas com Redis** (via Spring Cache / Spring Data Redis) na **Fase 2 (Pós-MVP)**.

### 1. Mecanismo de Funcionamento
* **Chave do Cache:** Hash SHA-256 gerado a partir da normalização do texto do tema (`texto_ou_tema` em minúsculas e sem acentuação).
* **Tempo de Vida (TTL):** As respostas armazenadas no Redis terão tempo de expiração padrão configurado para **24 horas**.
* **Estratégia de Invalidação:** Como a exegese histórica de um texto bíblico é imutável, a expiração por tempo (TTL) é suficiente. Atualizações na versão do prompt (versão da *System Instruction*) gerarão a invalidação automática de chaves antigas através de prefixos contendo a versão do prompt (ex: `exegese:v1.0:<hash>`).

### 2. Fluxo da Requisição com Cache
1. A requisição chega à camada de serviço (`ExegeseService`).
2. O sistema consulta o Redis com o hash da chave.
3. **Cache Hit:** Caso a resposta exista no Redis, o DTO é retornado instantaneamente ao cliente (< 10ms) sem acionar o Gemini.
4. **Cache Miss:** Caso não exista, a chamada síncrona ao Gemini é executada via `GenAiPort`, o resultado é gravado no Redis com o TTL configurado e retornado ao cliente.

## Consequências

### Positivas
* **Redução Drástica de Latência:** Respostas de temas repetidos passam de ~4 segundos para menos de 10ms.
* **Economia de Tokens e Custo:** Redução substancial de chamadas à API externa de IA, preservando as cotas do *Free Tier*.
* **Proteção contra Picos:** Evita sobrecarga da API externa em casos de picos de acessos em temas virais.

### Negativas
* Adiciona a necessidade de gerenciamento de uma instância Redis (ex: Redis Labs Free Tier ou Upstash Serverless Redis).
