# Diagrama de Estados das Entidades do Kanban

A seguir será apresentado o diagrama de estados que descreve o ciclo de vida dos **Quadros**, **Colunas** e **Tarefas** do kanban, incluindo os eventos que disparam as transições de estado e as condições de guarda que determinam a validade das operações.

> Estes diagramas são úteis para entender o comportamento das entidades ao longo do tempo e suas alterações dentro do banco de dados.

## Quadro

A validação do nome do Quadro ocorre de forma semelhante em `Criando Quadro` e `Editando Nome`, assumindo os seguintes critérios:

- O nome deve ser único (`Unique`).
- O nome não pode ser nulo (`Not Null`).
- O nome deve ter no máximo 50 caracteres (`String(50)`).

```mermaid
stateDiagram-v2
  Inexistente : Quadro não existe
  CriandoQuadro : Criando Quadro
  state CondicaoCriacao <<choice>>
  Ativo : Persistência no Banco
  Editando : Editando Nome
  state CondicaoEdicao <<choice>>
  Deletando : Deletando Quadro e todas as Colunas e Tarefas relacionadas

  [*] --> Inexistente : Início do Fluxo
  Inexistente --> CriandoQuadro : Solicitação de Criar Quadro
  CriandoQuadro --> CondicaoCriacao : Dados válidos?
  CondicaoCriacao --> Ativo : Sim <br> Novo Registro Criado
  CondicaoCriacao --> [*] : Não <br> Operação Abortada
  Ativo --> Editando : Solicitação de Renomear Quadro
  Editando --> CondicaoEdicao : Nome válido?
  CondicaoEdicao --> Ativo : Sim <br> Registro Atualizado
  CondicaoEdicao --> Ativo : Não <br> Rollback
  Ativo --> Deletando : Solicitação de Apagar Quadro
  Deletando --> [*] : Registro Eliminado
```

## Coluna

Fluxo semelhante ao de Quadro, seguindo os mesmos critérios de validação de nome, com a diferença de um novo atributo (`posição`) que também deve ser validado para garantir a unicidade *dentro do Quadro*:

- O nome deve ser único *dentro do Quadro* (`Unique`).
- O nome não pode ser nulo (`Not Null`).
- O nome deve ter no máximo 50 caracteres (`String(50)`).
- A posição deve ser única *dentro do Quadro* (`Unique`).

> **Nota:** A existência de colunas dependende necessariamente da existência de um quadro, portanto, não é possível criar uma coluna sem que exista um quadro.

```mermaid
stateDiagram-v2
  Inexistente : Coluna não existe
  QuantidadeMaxima : Analisando Quantidade de Colunas no Quadro
  state CondicaoMaxima <<choice>>
  CriandoColuna : Criando Coluna
  state CondicaoCriacao <<choice>>
  Ativo : Persistência no Banco
  Editando : Editando Coluna <br> (nome e/ou posição)
  state CondicaoEdicao <<choice>>
  QuantidadeMinima : Analisando Quantidade de Colunas no Quadro
  state CondicaoMinima <<choice>>
  Deletando : Deletando Coluna e todas as Tarefas relacionadas

  [*] --> Inexistente : Início do Fluxo
  Inexistente --> QuantidadeMaxima : Solicitação de Criar Coluna
  QuantidadeMaxima --> CondicaoMaxima : Quantidade de Colunas menor que 10?
  CondicaoMaxima --> CriandoColuna : Sim
  CondicaoMaxima --> [*] : Não <br> Operação Abortada
  CriandoColuna --> CondicaoCriacao : Dados válidos?
  CondicaoCriacao --> Ativo : Sim <br> Novo Registro Criado
  CondicaoCriacao --> [*] : Não <br> Operação Abortada
  Ativo --> Editando : Solicitação de Edição de Coluna
  Editando --> CondicaoEdicao : Dados válidos?
  CondicaoEdicao --> Ativo : Sim <br> Registro Atualizado
  CondicaoEdicao --> Ativo : Não <br> Rollback
  Ativo --> QuantidadeMinima : Solicitação de Apagar Coluna
  QuantidadeMinima --> CondicaoMinima : Quantidade de Colunas menor ou igual a 2?
  CondicaoMinima --> Deletando : Não
  CondicaoMinima --> Ativo : Sim <br> Operação Abortada
  Deletando --> [*] : Registro Eliminado
```

## Tarefa/Cartão

A validação do nome da Tarefa ocorre de forma semelhante em `Criando Tarefa` e `Editando Tarefa`, assumindo os seguintes critérios:

- O nome deve ser único *dentro do Quadro* (`Unique`).
- O nome não pode ser nulo (`Not Null`).
- O nome deve ter no máximo 50 caracteres (`String(50)`).
- A descrição pode ser nula (`Optional`).
- A descrição deve ter no máximo 255 caracteres (`String(255)`).
- A coluna deve existir e pertencer ao Quadro da Tarefa (`Foreign Key`).

> **Nota:** A existência de tarefas dependende necessariamente da existência de uma coluna, portanto, não é possível criar uma tarefa sem que exista uma coluna.

```mermaid
stateDiagram-v2
  Inexistente : Tarefa não existe
  CriandoTarefa : Criando Tarefa
  state CondicaoCriacao <<choice>>
  Ativo : Persistência no Banco
  Editando : Editando Tarefa <br> (nome, descrição e coluna)
  state CondicaoEdicao <<choice>>
  Deletando : Deletando Tarefa

  [*] --> Inexistente : Início do Fluxo
  Inexistente --> CriandoTarefa : Solicitação de Criar Tarefa
  CriandoTarefa --> CondicaoCriacao : Dados válidos?
  CondicaoCriacao --> Ativo : Sim <br> Novo Registro Criado
  CondicaoCriacao --> [*] : Não <br> Operação Abortada
  Ativo --> Editando : Solicitação de Edição de Tarefa
  Editando --> CondicaoEdicao : Dados válidos?
  CondicaoEdicao --> Ativo : Sim <br> Registro Atualizado
  CondicaoEdicao --> Ativo : Não <br> Rollback
  Ativo --> Deletando : Solicitação de Apagar Tarefa
  Deletando --> [*] : Registro Eliminado
```
