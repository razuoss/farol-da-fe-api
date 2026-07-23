# Farol da Fé API

Este projeto é uma API de apoio a estudos bíblicos exegéticos e devocionais cristãos via chat. O objetivo da ferramenta é auxiliar no entendimento e organização de ideias sobre textos e temas da fé. Embora seja um projeto piloto experimental, ele é desenvolvido com a seriedade e os padrões de engenharia de software prontos para produção.

---

## 📌 Propósito

O **Farol da Fé** visa apoiar cristãos com conteúdo informativo de qualidade para entendimento e estudos dos textos bíblicos, auxiliando no cruzamento de dados históricos, culturais e textuais.

O conteúdo gerado por este software destina-se **exclusivamente** ao apoio em estudos e devocionais com viés protestante histórico. **Não deve ser utilizado, sob nenhuma hipótese, como substituto do estudo pessoal da Bíblia, da oração e da direção do Espírito Santo, nem como conselho pastoral, jurídico, médico, psicológico ou normativo oficial de igrejas/denominações.**

---

## ⚠️ Aviso Legal e Isenção de Responsabilidade (Disclaimer)

1. **Uso de IA e Possibilidade de Erros:** As respostas são processadas através de modelos de Inteligência Artificial Generativa. Modelos de IA estão sujeitos a imprecisões, erros de interpretação ou alucinações. O usuário deve sempre validar qualquer conteúdo gerado diretamente no texto bíblico e com lideranças pastorais idôneas.
2. **Caráter Auxiliar:** Este software é uma ferramenta assistente. Nenhuma resposta gerada pela API possui caráter doutrinário, normativo ou dogmático.
3. **Isenção de Garantia ("AS IS"):** O software é fornecido "no estado em que se encontra" (*AS IS*), sem garantias de qualquer tipo, expressas ou implícitas. O autor não se responsabiliza por quaisquer danos, perdas ou decisões tomadas com base nas informações geradas por este sistema.
4. **Uso por Terceiros:** A replicação, execução ou hospedagem deste código por terceiros é de inteira responsabilidade de quem o fizer, devendo respeitar integralmente os termos da licença do projeto.

---

## 📜 Licença

Este projeto está licenciado sob a licença **Creative Commons Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional (CC BY-NC-SA 4.0)**. 

Você pode compartilhar e adaptar o material para fins não comerciais, desde que atribua o crédito ao projeto original e distribua sob a mesma licença. Veja detalhes em [LICENCE.md](LICENCE.md) e [TERMS.md](TERMS.md).

---

## 🛠️ Tecnologias Utilizadas

* **Java 21:** Linguagem de programação principal (Virtual Threads).
* **Spring Boot 3:** Framework para construção da API REST.
* **Maven:** Gerenciador de dependências e build.
* **Docker:** Conteinerização da aplicação.
* **Google Gemini API:** Provedor de IA Generativa.

---

## 📂 Estrutura do Projeto

```
├── docs/               # Documentação de arquitetura e ADRs
├── src/
│   ├── main/
│   │   ├── java/       # Código fonte da aplicação (Hexagonal)
│   │   └── resources/  # Prompts e configurações do Spring
│   └── test/           # Testes unitários e de integração
├── dockerfile
├── pom.xml
├── README.md
├── TERMS.md
└── LICENCE.md
```