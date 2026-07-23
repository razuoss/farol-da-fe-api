# ADR-008: Roadmap de Endpoints e Capacidades Teológicas

* **Status:** Aceito
* **Data:** 2026-07-22

## Contexto

Conforme o escopo do projeto Farol da Fé evoluiu de um gerador simples de devocionais para uma **Ferramenta de Apoio a Estudos Bíblicos Exegéticos**, fez-se necessário mapear e formalizar os contratos de endpoints de domínio e capacidades teológicas futuras.

O objetivo é garantir a assertividade da interpretação textual bíblica (contexto histórico, público-alvo, idiomas originais e aplicação prática), evitando erros decorrentes de tradições humanas desprovidas de fundamentação exegética.

## Decisão

Foi definida a padronização do endpoint principal do MVP como `POST /v1/exegese` e o planejamento de novos endpoints de domínio especializados:

### 1. Endpoint Principal do MVP: `POST /v1/exegese`
* **Substituição do termo:** O nome do endpoint de domínio foi ajustado de `/explicacao` para `/exegese`, por refletir com maior precisão técnica a extração do significado original do texto (contexto, público, análise textual e aplicação prática).

### 2. Endpoints de Expansão (Roadmap Futuro)
* **`POST /v1/compara-traducoes` (Comparador de Traduções e Idioma Original):**
  * Endpoint focado em comparar passagens bíblicas nas principais traduções (ex: ACF - Equivalência Formal vs. NVT/NVI - Equivalência Dinâmica), destacando termos relevantes no grego (Koiné) ou hebraico com explicações acessíveis.
* **`POST /v1/devocional` (Meditação e Edificação Pessoal):**
  * Endpoint voltado para meditação diária, aplicação ao coração e sugestão de oração pessoal.
* **`POST /v1/sermao` (Esboço de Sermão Expositivo):**
  * Endpoint para apoio a pastores e pregadores, gerando a ideia central do texto, introdução, pontos expositivos, correlações bíblicas e aplicação para a congregação.

## Consequências

### Positivas
* **Clareza Semântica:** A nomenclatura `/v1/exegese` eleva o nível técnico da documentação e comunica o verdadeiro propósito exegético da API.
* **Planejamento Arquitetural:** Permite evoluir os contratos de API sem quebrar a retrocompatibilidade nem misturar responsabilidades em um único endpoint gigante.
* **Reuso de Código:** Todos os novos endpoints reutilizarão as mesmas portas (`GenAiPort`, `AuditRepositoryPort`) e a infraestrutura de segurança/guardrail.

### Negativas
* Necessidade de manutenção de novos DTOs de entrada e saída à medida que os novos endpoints forem desenvolvidos no backlog.
