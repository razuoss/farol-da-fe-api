# ADR-007: Definição de Métricas de Serviço e Estimativas de Carga

* **Status:** Proposto
* **Data:** 2026-07-21

## Contexto

Este documento define os requisitos não-funcionais (NFRs) e as metas de serviço (SLOs) para a **Fase 2** do projeto, quando a aplicação for migrada para o **Google Cloud Run**, conforme definido no `ADR-004`. O objetivo é garantir que a arquitetura no GCP opere de forma confiável, performática e dentro dos limites da camada gratuita ("Always Free").

## Decisão

Foram definidas as metas de serviço (SLOs), a estimativa de carga, e as estratégias de resiliência para o MVP.

**Análise das Cotas "Always Free" do Cloud Run:**

* **Requisições:**
    * **Cota:** 2.000.000 / mês
    * **Consumo Estimado:** 90.000 / mês
    * **Resultado:** **Seguro.** (Utilização de ~4.5%)

* **Memória:**
    * **Cota:** 360.000 GiB-segundos / mês
    * **Consumo Estimado:** 90.000 req * 2s/req * 0.5 GiB de RAM = 90.000 GiB-segundos / mês
    * **Resultado:** **Seguro.** (Utilização de 25%)

* **CPU:**
    * **Cota:** 180.000 vCPU-segundos / mês
    * **Consumo Estimado:** 90.000 req * 2s/req * 1 vCPU = 180.000 vCPU-segundos / mês
    * **Resultado:** **No Limite.** (Utilização de 100%)

### 1. SLOs e Estimativa de Carga (1.000 usuários)
* **SLOs:**
    * **Custo:** Operar em um modelo "near-zero cost", com um orçamento de segurança de R$ 10,00/mês no GCP.
    * **Performance:** `p95 < 15 segundos` para as respostas da API (excluindo cold start).
    * **Confiabilidade:** `api_error_rate < 0.1%`.
* **Estimativa de Carga:** Pico de **~10 requisições/minuto**.
* **Análise de Consumo:** A análise detalhada da cota gratuita do Cloud Run, considerando a alocação de CPU apenas durante o processamento, valida que a carga estimada se encaixa nos limites, com a cota de CPU sendo o recurso mais crítico a ser monitorado.

### 2. Estratégias de Resiliência e Controle
* **Controle de Custos:** Será criado um **Orçamento de Faturamento** no GCP de R$ 10,00, com uma ação programática para **desativar o faturamento** do projeto caso o limite seja atingido, funcionando como uma trava de segurança.
* **Rate Limiting:** Será implementado na aplicação um limitador de taxa (rate limiter) em memória (via `Bucket4j`) para proteger contra picos de tráfego, abuso e requisições em loop. A meta inicial será de 4 requisições por minuto por IP.
* **Timeouts:** Será implementado um **timeout de 30 segundos** na chamada para a API externa do Gemini (via `Resilience4j`). Se a chamada exceder este tempo, a aplicação irá interrompê-la e retornar um erro controlado (`504 Gateway Timeout`), evitando que recursos fiquem presos.
* **Otimização de Concorrência:** A aplicação será configurada para utilizar **Virtual Threads (Java 21)**, e o serviço no Cloud Run terá seu máximo de solicitações simultâneas por instância ajustado para **10**.

## Consequências

### Positivas
* O projeto é concebido com resiliência desde o início, em vez de ser uma preocupação tardia.
* O risco financeiro é ativamente gerenciado e limitado a um valor simbólico e controlado.
* A arquitetura da aplicação é otimizada para tarefas I/O-bound, garantindo o uso eficiente dos recursos de CPU e o cumprimento das cotas da camada gratuita.
* As decisões tornam a aplicação mais robusta contra comportamentos inesperados (picos de tráfego, lentidão de APIs externas).

### Negativas
* A implementação de resiliência (Rate Limiting, Timeouts) adiciona novas dependências (`Bucket4j`, `Resilience4j`) e aumenta ligeiramente o escopo de desenvolvimento do MVP.