# ADR-003: Estratégia de Branching e Fluxo de Trabalho (Git)

* **Status:** Aceito
* **Data:** 2025-10-07
* **Update:** 2026-07-21

## Contexto

Para organizar o desenvolvimento e o processo de deploy, fez-se necessária uma estratégia de branching no Git. O modelo GitFlow completo (com branches `feature`, `develop`, `release`, `hotfix` e `main`) foi considerado por ser um padrão robusto da indústria. No entanto, para a fase inicial de um projeto com um único desenvolvedor, a complexidade e a burocracia das branches `release` foi considerada um potencial obstáculo à agilidade.

## Decisão

Adotaremos o padrão **"GitFlow Simplificado"** para a fase de MVP.

* **`main`:** Branch principal, reflete o código em **Produção**. Será protegida para aceitar merges apenas da branch `develop` e via Pull Requests com aprovação.

* **`develop`:** Branch de integração, reflete o estado "pronto para homologação". Será a branch padrão do repositório e também será protegida para aceitar merges apenas de branches `feature/*` via Pull Requests.

* **`feature/*`:** Branches para desenvolvimento de novas funcionalidades ou correções. São criadas a partir da `develop` e devem ser mescladas de volta para a `develop` via Pull Request.

O fluxo de trabalho padrão será: `feature/*` → `develop` (Homologação) → `main` (Produção).

A implementação de branches `release/*` será registrada como uma dívida técnica no backlog, a ser reavaliada quando o projeto atingir maior maturidade ou houver múltiplos contribuidores.

## Consequências

### Positivas
* O fluxo de trabalho é ágil e otimizado para a entrega rápida de features.
* A estrutura é simples de entender e gerenciar, reduzindo a carga cognitiva.
* A obrigatoriedade de Pull Requests para `develop` e `main` garante a execução do pipeline de CI e a possibilidade de revisão de código.
* **Mitigação de Risco:** Ao integrar todas as features na branch `develop` antes de promovê-la para a `main`, garantimos que o código seja testado de forma integrada no ambiente de homologação. Isso reduz drasticamente o risco de conflitos e bugs de integração surgirem apenas em produção.

### Negativas
* O processo de versionamento formal (ex: preparar a versão `v1.1.0`) é menos estruturado. Este é um trade-off aceitável para as fases iniciais do projeto.
* **Acoplamento de Release:** Features prontas podem ter seu deploy para produção atrasado por outras features que foram mescladas em `develop` mas apresentaram problemas na homologação. Este é um trade-off consciente em favor da estabilidade da branch `main`.