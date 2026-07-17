# 🏁 Fase 00: Engenharia de Requisitos e Modelagem Conceitual

---

## 🎯 1. Escopo Lógico e Mecânicas do Kanban

### Entidades do Domínio e Restrições Estruturais

* **Quadro:** Agrupador máximo do ecossistema. O sistema gerencia múltiplos quadros anônimos isolados.
* **Coluna:** Elemento relacional de segmentação de fluxo vinculado a um único quadro.
* **Tarefa:** Unidade atômica de trabalho vinculada a uma única coluna.

### Regras de Customização e Estado das Colunas

* **Estado Inicial:** Cada novo quadro nascerá obrigatoriamente com três colunas padrão: `A Fazer`, `Em Andamento` e `Concluído`.
* **Mutabilidade:** O usuário possui autonomia para renomear, adicionar ou remover colunas.
* **Restrição Limite:** Um quadro deve conter, obrigatoriamente, um intervalo dinâmico de no mínimo **2** e no máximo **10 colunas** simultâneas. Qualquer tentativa de quebrar esse limite via requisição HTTP deve ser rejeitada pelo backend.

### Anatomia e Regras de Validação da Tarefa

* **Título:** Texto obrigatório, limitado a no máximo **50 caracteres**.
* **Responsável:** Texto opcional, limitado a no máximo **50 caracteres** (tratado como string simples, sem relacionamento de tabelas de autenticação).
* **Descrição:** Texto opcional, limitado a no máximo **255 caracteres**.
* **Prioridade:** Campo obrigatório com restrição do tipo Enumerador estrito: `Alta`, `Média` ou `Baixa`.
* **Data de Vencimento:** Campo opcional armazenando data/timestamp simples.
* **Ordenação:** Fila simples e não-indexada. Novas tarefas criadas ou movidas caem obrigatoriamente na última posição (final da fila) da coluna de destino.

---

## 📊 2. Especificação de Engenharia de Software

### Tabela Geral de Requisitos (Rastreabilidade)

| ID | Tipo | Nome do Requisito | Descrição Pragmática | Prioridade |
| --- | --- | --- | --- | --- |
| **RF01** | Funcional | Múltiplos Quadros | O sistema deve permitir criar, listar e deletar quadros Kanban de forma independente por ID e Título. | Alta |
| **RF02** | Funcional | Colunas Dinâmicas | O sistema deve gerenciar o ciclo de vida de colunas atreladas a um quadro, respeitando o teto de 2 a 10 elementos. | Alta |
| **RF03** | Funcional | Movimentação de Tarefas | O sistema deve permitir criar tarefas e transicionar sua alocação entre colunas pertencentes ao mesmo quadro. | Alta |
| **RNF01** | Não-Func. | Telemetria Mandatória | 100% das rotas de leitura/escrita devem propagar o identificador `trace_id` em logs estruturados em formato JSON. | Crítica |
| **RNF02** | Não-Func. | Validação Server-Side | O backend deve rejeitar transações lógicas inválidas retornando código de status HTTP 400 (Bad Request). | Crítica |
| **RNF03** | Não-Func. | Persistência Relacional | A exclusão de entidades pai deve acionar o expurgo imediato em cascata física de dados no banco de dados (Hard Delete). | Alta |

### Detalhamento dos Casos de Uso Críticos

#### CSU01: Inclusão/Exclusão Dinâmica de Colunas (Validação de Teto)

* **Ator Principal:** Usuário anônimo/Cliente HTTP.
* **Pré-condição:** O quadro correspondente deve existir no banco de dados.
* **Fluxo Principal (Adição):**
  1. O cliente solicita a criação de uma nova coluna enviando o ID do quadro.
  2. O backend conta o número de colunas ativas daquele quadro.
  3. A contagem atual é menor que 10.
  4. O sistema persiste a nova coluna e retorna HTTP 201 (Created).
* **Fluxo de Erro A (Estouro Máximo):**
  1. No passo 3 do fluxo principal, a contagem de colunas é igual a 10.
  2. O backend aborta a transação e retorna `HTTP 400 Bad Request`.
  3. O frontend intercepta o erro e exibe um pop-up de alerta: *"Limite máximo de 10 colunas atingido"*.
* **Fluxo de Erro B (Estouro Mínimo):**
  1. O cliente envia uma requisição `DELETE` para remover uma coluna.
  2. O backend conta as colunas ativas daquele quadro e identifica que restam apenas 2.
  3. O backend aborta a remoção e retorna `HTTP 400 Bad Request`.
  4. O frontend exibe um pop-up de alerta: *"O quadro deve conter no mínimo 2 colunas"*.

#### CSU02: Exclusão Relacional em Cascata (Hard Delete)

* **Pré-condição:** Existência de colunas com tarefas associadas.
* **Fluxo Principal:**
  1. O usuário aciona o comando de deletar uma coluna ou um quadro inteiro.
  2. O frontend exibe uma confirmação nativa do navegador (`confirm()`).
  3. O usuário clica em "Confirmar".
  4. O frontend dispara a requisição HTTP correspondente.
  5. O banco de dados executa a limpeza imediata das tarefas órfãs via restrição física `ON DELETE CASCADE`.
  6. O sistema retorna HTTP 200/204.

---

## 📐 3. Artefatos de Modelagem (Documentação Viva)

* **[Diagrama de Estados (Mermaid):](../arquitetura/estados.md)** Determina o ciclo de vida e caminhos permitidos para as tarefas entre as colunas mutáveis do mesmo quadro.
* **[Diagrama de Classes (Mermaid):](../arquitetura/classes.md)** Desenho estrutural das camadas de domínio e casos de uso orientados à Arquitetura Limpa.
* **[Modelo Lógico do Banco de Dados (DBML):](../arquitetura/bd_logico.dbml)** Mapeamento relacional de tabelas e restrições de chaves estrangeiras.

---

## 🐙 4. Governança e Organização do GitHub

* **Rastreabilidade via Issues:** O planejamento conceitual e a engenharia de requisitos foram fatiados em Issues atômicas no GitHub para evitar sobreposição de escopo.
* **Vínculo de Progresso (Commits):** Cada commit realizado deve referenciar a issue correspondente no corpo ou na mensagem do commit (ex: `docs: adoeqca requisitos da fase 00 #1`).
* **Definition of Done (DoD) - Critério de Sucesso da Fase 00:**
* Ausência de erros de sintaxe ou renderização nos arquivos Mermaid.
* Links internos de navegação totalmente operacionais entre o README e a pasta `/docs`.
* Aprovação e fechamento automático das Issues mapeadas através do merge do Pull Request (PR) unificado na branch `main`.

---

## 📝 5. Lições Aprendidas e Custo de Oportunidade Conceitual

> Preenchido de forma empírica pelo desenvolvedor ao fechar o Pull Request de documentação
