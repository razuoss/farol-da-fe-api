# ADR-009: Estratégia de Transição da Auditoria (Google Sheets para MongoDB)

* **Status:** Proposto
* **Data:** 2026-07-22

## Contexto

No MVP da aplicação Farol da Fé, a gravação de requisições e estudos exegéticos para auditoria e curadoria teológica é realizada de forma assíncrona (`@Async`) na planilha do **Google Sheets** via `GoogleSheetsAdapter`, visando agilidade de implementação e custo zero (ADR-004).

À medida que o sistema evoluir em volume de requisições e necessidade de consultas estruturadas (ex: busca por versículos mais pesquisados, filtragem por data ou auditoria de respostas), o Google Sheets apresentará limitações de taxa (Rate Limits da API do Google), cota e concorrência.

## Decisão

Foi definida a estratégia de transição do mecanismo de auditoria para o **MongoDB** na **Fase 2 (Pós-MVP)**, sustentada pelo padrão de Arquitetura Hexagonal da aplicação.

### 1. Papel do MongoDB
* O MongoDB armazenará documentos BSON na coleção `auditoria_estudos` contendo:
  * ID da requisição e identificador do usuário.
  * Texto ou tema bíblico pesquisado.
  * Resposta estruturada retornada pela IA.
  * Metadados de execução (latência da IA em ms, versão do prompt, provedor utilizado e data/hora UTC).

### 2. Padrão de Desacoplamento
* A regra de negócio permanecerá intacta, pois interage exclusivamente com a interface `AuditRepositoryPort`.
* A migração consistirá na criação da classe `MongoDbAuditAdapter` implementando `AuditRepositoryPort`, alternando o Spring Bean via perfil de configuração (`@Profile("prod")` ou propriedade `app.audit.provider=mongodb`).

## Consequências

### Positivas
* **Escalabilidade e Concorrência:** O MongoDB suporta elevado volume de gravações simultâneas sem bloqueios ou estoiro de cotas da API de planilhas.
* **Flexibilidade de Esquema:** Como cada estudo gera uma resposta JSON estruturada, o modelo de documentos NoSQL do MongoDB é ideal para salvar e indexar esses dados sem necessidade de migrações rígidas de tabelas.
* **Capacidade de Query e Analytics:** Permite realizar consultas complexas de curadoria, identificar temas bíblicos mais pesquisados e analisar métricas de uso.

### Negativas
* Adiciona a necessidade de provisionar uma instância de banco de dados (ex: MongoDB Atlas Free Tier - 512MB ou contêiner dedicado).
