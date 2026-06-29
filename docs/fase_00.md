# 🏁 Fase 00: Engenharia de Requisitos e Modelagem Conceitual

---

## 🎯 1. Escopo Lógico e Mecânicas do Kanban

- **Entidades do Domínio:**
  - **Quadro (Board):** Agrupador máximo do ecossistema.
  - **Coluna (Column):** Elemento de segmentação de fluxo.
  - **Tarefa (Task/Card):** Unidade atômica de trabalho.
- **Regras de Customização e Estado das Colunas:**
  - *Estado Inicial:* O quadro nascerá obrigatoriamente com três colunas padrão: `To Do`, `In Progress` e `Done`.
  - *Mutabilidade:* O usuário possui autonomia para renomear, adicionar novas colunas ou remover colunas existentes.
  - *Restrição Limite:* O quadro deve conter, obrigatoriamente, um intervalo dinâmico de no mínimo **2** e no máximo **10 colunas** simultâneas.
- **Anatomia e Regras da Tarefa (Card):**
  - *Campo Obrigatório:* `Título` (Texto).
  - *Campos Opcionais:* `Descrição` (Texto), `Data de Vencimento` (Timestamp/Date), `Prioridade` (Alta/Média/Baixa) e `Responsável` (Texto).
  - *Isolamento de Entidade:* O campo `Responsável` será tratado estritamente como um campo de texto plano (String), mitigando a necessidade de uma entidade de usuário isolada no banco de dados.

---

## 📐 2. Artefatos de Modelagem (Documentação Viva)

- [**Diagrama de Estados (Mermaid):**](estados.md) Determina o ciclo de vida e caminhos permitidos para os cards entre as colunas mutáveis.
- [**Diagrama de Classes (Mermaid):**](classes.md) Desenho estrutural das camadas de domínio e casos de uso orientados à Arquitetura Limpa.
- [**Modelo Lógico do Banco de Dados:**](bd_logico.dbml) Mapeamento relacional de tabelas locais para SQLite.

---

## 🐙 3. Governança e Organização do GitHub

- **Rastreabilidade via Issues:** O planejamento conceitual e a engenharia de requisitos foram fatiados em Issues atômicas no GitHub para evitar sobreposição de escopo.
- **Vínculo de Progresso (Commits):** Cada commit realizado deve referenciar a issue correspondente no corpo ou na mensagem do commit (ex: `docs: desenha fluxo de transição no mermaid #1`) para manter o gráfico de progresso do GitHub atualizado.
- **Definition of Done (DoD) - Critério de Sucesso da Fase 00:**
  - Ausência de erros de sintaxe ou renderização nos arquivos Mermaid.
  - Links internos de navegação totalmente operacionais entre o README e a pasta `/docs`.
  - Aprovação e fechamento automático das 5 Issues mapeadas através do merge do Pull Request (PR) unificado na branch `main` utilizando palavras-chave de fechamento (ex: `Closes #1, Closes #2`).

---

## 📝 4. Lições Aprendidas e Custo de Oportunidade Conceitual

> (Preenchido de forma empírica pelo desenvolvedor ao fechar o Pull Request de documentação)
