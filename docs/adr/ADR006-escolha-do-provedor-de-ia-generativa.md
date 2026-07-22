# ADR-006: Escolha do Provedor de IA Generativa (LLM)

* **Status:** Aceito
* **Data:** 2026-07-21

## Contexto

A API Farol da Fé necessita de um provedor de IA Generativa (LLM) capaz de processar solicitações exegéticas e de análise textual bíblica, retornando dados estritamente estruturados em JSON.

As opções principais avaliadas foram:
1. **Google Gemini API (versões Flash)**
2. **OpenAI API (versões GPT-xx-mini)**
3. **Anthropic Claude API (versões Haiku)**
4. **Execução Local / Self-Hosted (Ollama / Qwen 3)**

### Requisitos e Restrições:
* **Custo:** Necessidade de operar preferencialmente dentro de uma camada gratuita (Free Tier) ou a custos de centavos (near-zero cost).
* **Estruturação de Dados (Structured Output):** Suporte nativo e garantido a respostas formatadas via JSON Schema.
* **Recursos de Segurança (System Instruction):** Suporte a instruções de sistema isoladas e protegidas contra *Prompt Injection*.
* **Desempenho e Latência:** Tempo de resposta baixo (< 5 segundos p95).

## Decisão

Adotaremos o **Google Gemini (família Flash)** como o provedor principal de IA Generativa para o MVP, abstraído através da porta `GenAiPort` na Arquitetura Hexagonal da aplicação.

### Justificativa:
1. **Generosidade da Camada Gratuita (Free Tier):** O Google AI Studio oferece limites gratuitos extremamente generosos, perfeitamente compatíveis com as estimativas do MVP.
2. **Suporte Nativo a JSON Schema (Structured Output):** O Gemini permite passar um esquema JSON (OpenAPI 3.0 schema) diretamente no parâmetro de configuração da requisição, garantindo que o modelo responda estritamente no formato tipado esperado pela aplicação Java.
3. **Suporte Nativo a `system_instruction`:** Permite separar completamente as diretrizes de conduta exegética e restrições de segurança do prompt do usuário, mitigando riscos de *jailbreak*.
4. **Sinergia com Ecossistema GCP:** Futura integração facilitada no Cloud Run e Vertex AI caso haja necessidade de migração enterprise.

## Consequências

### Positivas
* Manutenção do custo zero (ou near-zero) durante toda a fase de validação do MVP.
* Redução drástica do parsing e tratamento de erros no backend, pois o Gemini garante o retorno JSON conforme o schema.
* Baixa latência nas respostas utilizando os modelos da linha Flash.

### Negativas
* **Dependência do Fornecedor (Vendor Lock-in mitigation):** Para evitar acoplamento, a aplicação **não expõe** bibliotecas ou estruturas do Gemini na camada de domínio. A comunicação ocorre exclusivamente através da interface/porta `GenAiPort`, permitindo a troca para OpenAI ou outro provedor via configuração de Spring Bean caso necessário no futuro.