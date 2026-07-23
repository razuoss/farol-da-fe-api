# ADR-004: Plataforma de Hospedagem e Estratégia de Containerização

* **Status:** Aceito
* **Data:** 2025-10-07
* **Update:** 2026-07-21

## Contexto

AA seleção da plataforma de nuvem é uma decisão fundamental que impacta custo, performance, complexidade e o fluxo de trabalho de CI/CD. Diversas opções foram consideradas, dentre as principais uso 100% no Render, uma abordagem híbrida Render+ , Render + GCP, 100% Azure e 100% GCP. Cada opção apresentava trade-offs significativos. A principal restrição é a necessidade de operar em uma camada mais robusta possível que menor custo, com suporte a contêineres Docker e que permitisse a segregação de ambientes de Produção e Homologação.

As principais opções PaaS/CaaS (como Render e Google Cloud Run) foram analisadas. A análise crítica se concentrou em:
1.  **Modelo de Custo:** A generosidade e o funcionamento da camada gratuita.
2.  **Recursos de Hardware:** Limites de CPU e, crucialmente, de Memória RAM (512MB no Render vs. alocação dinâmica no GCP).
3.  **Curva de Aprendizado e Velocidade de Deploy:** Facilidade de configuração inicial.
4.  **Integração com Ecossistema:** Facilidade de integração com outros serviços (neste caso, Google Sheets).

    * Render - Plano Hobby (Gratuito):
        - Instâncias: 1 instância/projeto por conta.
        - Tempo de Atividade: 750 horas de build e 750 horas de execução por mês (compartilhadas).
        - Memória: 512 MB.
        - CPU: 0.1 vCPU.
        - Cold Start - serviço é suspenso após 15 minutos de inatividade, com tempo de retorno estimado em 50 segundos.

    * Google Cloud Plataform - Cloud Run ("Always Free"):
        - Requisições: 2 milhões/mês
        - Memória: 360.000 GiB-segundos/mês
        - CPU: 180.000 vCPU-segundos/mês
        - Cold Start - serviço é suspenso por inatividade, mas o tempo de retorno é estimado em 1 segundo.

    * Azure: 
        Functions (Plano de Consumo - Gratuito):
        - Requisições: 1 milhão/mês
        - Tempo de Execução: 400.000 GB-segundos/mês
        - Ideal para trechos de código pequenos e reativos (serverless). Cold Start variável, mas geralmente rápido para funções simples.

        App Service (Plano Gratuito - F1):
        - Instâncias: 1 instância compartilhada.
        - Memória: 1 GB.
        - CPU: Compartilhada.
        - Hospedagem de aplicações web completas. Cold Start presente, mas com tempo de retorno geralmente menor que o Render.

Cenários estimados:

    Requisições: ~90.000 req/mês (~3.000 req/dia)

    CPU e Memória (A Análise Crítica):
        Estimativa de pico em 15 req/min.

        Uma requisição para a IA pode levar 20 segundos a depender do modelo escolhido. Se a CPU ficasse alocada durante todo esse tempo, nosso consumo seria 90.000 requisições * 20 segundos = 1.800.000 vCPU-segundos. Isso estouraria as cotas de processamento.

        O Google Cloud Run tem uma configuração de alocação de CPU. Por padrão, a CPU só é alocada enquanto o seu código está ativamente processando dados. Durante a maior parte desses 20 segundos, nossa aplicação estará apenas esperando a resposta da API do Gemini (uma operação de I/O). Durante o I/O, o Cloud Run não aloca (e não cobra pela) CPU.

        O processamento real do nosso código (receber a requisição, montar o prompt, tratar a resposta) levará, talvez, 1 a 2 segundos por requisição. Com isso, nosso consumo seria de 90.000 requisições * 2 segundo = 180.000 vCPU-segundos. Caso fosse escolhida a GCP, estaremos no limite porém atendendo satisfatoriamente.

## Decisão

Adotaremos a **containerização da aplicação com Docker** e uma **estratégia de hospedagem em duas fases**:

1.  **Fase 1 (MVP): Hospedagem no Render.**
    *   **Justificativa:** Priorizar a velocidade de desenvolvimento e a entrega rápida de um MVP funcional. A simplicidade e a baixa curva de aprendizado do Render são ideais para esta fase, permitindo foco total na lógica da aplicação.
    *   **Risco Aceito:** O limite de 512MB de RAM é um risco conhecido. A aplicação será monitorada e, caso se torne um gargalo, a migração para o GCP será acelerada.

2.  **Fase 2 (Pós-MVP): Migração para o Google Cloud Platform (GCP).**
    *   **Gatilho:** Após a validação do MVP ou caso o limite de recursos do Render se torne um problema.
    *   **Justificativa:** Adotar uma plataforma mais robusta, escalável e com melhor integração com o ecossistema Google (Sheets, etc.). A migração será tratada como uma dívida técnica planejada.

O Render foi escolhido para a fase inicial por ser de fácil configuração e atender satisfatoriamente as premissas. SUas vantagens são, mas não limitadas a: 
1.  **Facilidade de Uso:** Interface intuitiva e configuração simplificada, permitindo um deploy rápido e sem a necessidade de grande conhecimento em infraestrutura.
2.  **Foco no Desenvolvimento:** Permite que o desenvolvedor se concentre na lógica de negócio da aplicação, abstraindo a complexidade da infraestrutura.
3.  **Ambiente de Teste Rápido:** Ideal para validar funcionalidades e coletar feedback inicial de forma ágil.

O Google Cloud Run foi escolhido como plataforma única por:
1.  **Modelo Serverless:** Sua arquitetura "scale-to-zero" e camada gratuita generosa garantem a viabilidade do custo zero.
2.  **Performance:** Oferece um tempo de "cold start" para contêineres Java superior às alternativas de PaaS analisadas.
3.  **Valor de Portfólio:** Demonstra profundidade e competência em um dos três maiores e mais requisitados provedores de nuvem do mercado.

## Consequências

### Positivas
* **Agilidade no MVP:** A decisão de iniciar no Render acelera a entrega da primeira versão funcional do projeto.
* **Decisão Baseada em Dados:** A migração para o GCP será feita com base na necessidade real, não apenas em uma premissa teórica.
* **Foco no Produto:** Permite que o esforço inicial seja concentrado no desenvolvimento da API, não em infraestrutura complexa.

### Negativas
* **Dívida Técnica:** A necessidade de uma futura migração é criada e deve ser gerenciada no backlog do projeto.
* **Risco de Instabilidade:** Existe a chance de a aplicação apresentar problemas de performance ou memória no Render antes do planejado, forçando uma migração reativa.
* **Inconsistência Temporária:** Durante a fase 1, a integração com o Google Sheets será menos segura (via arquivo de credenciais) do que a abordagem final planejada para o GCP.