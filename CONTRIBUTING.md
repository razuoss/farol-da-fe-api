# Guia de Padrões e Contribuições - Farol da Fé

Agradecemos o seu interesse no projeto Farol da Fé!

**Status Atual do Projeto:**
atualmente, o projeto está em sua fase inicial de desenvolvimento (MVP), sendo construído por seu mantenedor principal. Neste momento, **não estamos aceitando contribuições externas de código**. O objetivo é estabelecer uma base sólida e uma primeira versão funcional que servirá como peça de portfólio.

No futuro, após o lançamento do MVP, o projeto será aberto para colaboração.

Este documento serve como um registro dos padrões de engenharia que seguimos e que serão exigidos para todas as futuras contribuições.

---

## 🚀 Como Rodar o Projeto Localmente

1.  **Faça um Fork** do repositório para a sua própria conta no GitHub.
2.  **Clone** o seu fork para a sua máquina local: `git clone https://github.com/SEU-USUARIO/farol-da-fe.git`
3.  **Instale as dependências** do projeto: `mvn clean install`
4.  **Execute a aplicação** localmente para garantir que tudo está funcionando: `mvn spring-boot:run`

## 🌿 Fluxo de Trabalho (GitFlow Simplificado)

Quando as contribuições forem abertas, seguiremos um fluxo de trabalho simplificado baseado no GitFlow.

1.  **Sincronize sua `develop`:** Antes de começar, garanta que sua branch `develop` local está atualizada com o repositório principal.
2.  **Crie uma `feature branch`:** Crie uma nova branch a partir da `develop`. O nome deve ser descritivo, prefixado com `feature/`:
    ```bash
    git checkout develop
    git pull origin develop
    git checkout -b feature/nome-da-sua-feature
    ```
3.  **Desenvolva e Faça Commits:** Trabalhe na sua feature e faça commits seguindo o nosso padrão de mensagens.
4.  **Abra um Pull Request (PR):** No GitHub, abra um Pull Request da sua `feature branch` para a branch `develop` do repositório principal para revisão.

## ✍️ Padrão de Mensagens de Commit (Conventional Commits)

Todas as mensagens de commit **devem** seguir o padrão **Conventional Commits** para manter um histórico limpo e legível.

A estrutura é: `<tipo>(<escopo>): <descrição>`

#### Guia Rápido de Tipos de Commit

| Tipo         | Quando Usar                                                   | Exemplo                                                |
| :----------- | :------------------------------------------------------------ | :----------------------------------------------------- |
| **`feat`** | Adicionar uma nova funcionalidade.                            | `feat(api): add endpoint geração de devocinais em audio` |
| **`fix`** | Corrigir um bug.                                              | `fix(service): lidar com temas nulos de forma elegante`           |
| **`ci`** | Alterar os arquivos de workflow (`.github/workflows/`).        | `ci(actions): adicionar job para deploy em produção`     |
| **`docs`** | Mudar o `README.md` ou adicionar um ADR.                      | `docs(adr): adicionar ADR para decisão de plataforma de hospedagem`      |
| **`refac`**| Melhorar o código sem mudar o que ele faz.                    | `refac(service): extrair chamada Gemini para um adaptador separado`|
| **`chore`** | Tarefas de manutenção (ajustar `pom.xml`, `.gitignore`).      | `chore(build): atualizar versão do Spring Boot no pom.xml`   |
| **`style`** | Mudanças de formatação que não afetam o código.               | `style(controller): aplicar formatação de código`              |
| **`test`** | Adicionar ou corrigir testes.                                 | `test(service): adicionar testes unitários para geração de devocionais`|

## 📐 Padrões de Código

* **Formatação e Qualidade:** O projeto utiliza ferramentas de análise estática (Checkstyle, SpotBugs) para garantir a consistência e a qualidade do código.
* **Garantia:** Antes de enviar qualquer alteração, é mandatório que o build local passe sem erros executando `mvn clean verify`. O pipeline de CI irá validar esta condição.

---
*Este documento poderá ser atualizado a qualquer momento.*