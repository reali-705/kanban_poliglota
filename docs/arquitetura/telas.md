# Fluxo de Telas do Sistema

```mermaid
stateDiagram-v2
  TelaInicial : Tela Inicial (Dashboard)
  TelaQuadro : Tela do Quadro Kanban
  state CondicaoNavegacao <<choice>>

  [*] --> TelaInicial : Acesso ao Sistema
  TelaInicial --> CondicaoNavegacao : Usuário clica em um Quadro?
  CondicaoNavegacao --> TelaQuadro : Sim
  CondicaoNavegacao --> [*] : Sair do Sistema
  TelaQuadro --> TelaInicial : Clica em Voltar
  TelaQuadro --> [*] : Sair do Sistema

  state TelaInicial {
    VisualizandoLista : Lista de Quadros
    ModalQuadro : Modal de Quadro (Criar/Editar)
    state CondicaoSubmitQuadro <<choice>>
    ModalDeletarQuadro : Alerta de Exclusão

    [*] --> VisualizandoLista
    
    VisualizandoLista --> ModalQuadro : Clica em Novo ou Editar
    ModalQuadro --> CondicaoSubmitQuadro : Submete Formulário
    CondicaoSubmitQuadro --> ModalQuadro : Falha (Validação Front ou API)
    CondicaoSubmitQuadro --> VisualizandoLista : Sucesso
    ModalQuadro --> VisualizandoLista : Clica em Cancelar

    VisualizandoLista --> ModalDeletarQuadro : Clica em Excluir
    ModalDeletarQuadro --> VisualizandoLista : Confirma ou Cancela
  }

  state TelaQuadro {
    VisualizandoQuadro : Colunas e Cartões Visíveis
    
    %% Modais de Coluna
    ModalColuna : Modal de Coluna (Criar/Editar)
    state CondicaoSubmitColuna <<choice>>
    ModalDeletarColuna : Alerta Crítico (Exclusão em Cascata)
    
    %% Modais de Tarefa
    ModalTarefa : Modal de Tarefa (Criar/Editar)
    state CondicaoSubmitTarefa <<choice>>
    ModalDeletarTarefa : Alerta de Exclusão de Tarefa
    
    %% Fluxo de Movimentação (Drag and Drop)
    ArrastandoCartao : Arrastando Cartão
    state CondicaoDrop <<choice>>

    [*] --> VisualizandoQuadro

    %% Fluxo Visual de Coluna
    VisualizandoQuadro --> ModalColuna : Clica em Nova/Editar Coluna
    ModalColuna --> CondicaoSubmitColuna : Submete
    CondicaoSubmitColuna --> ModalColuna : Falha
    CondicaoSubmitColuna --> VisualizandoQuadro : Sucesso
    ModalColuna --> VisualizandoQuadro : Clica em Cancelar

    VisualizandoQuadro --> ModalDeletarColuna : Clica em Excluir Coluna
    ModalDeletarColuna --> VisualizandoQuadro : Confirma Deleção ou Cancela

    %% Fluxo Visual de Tarefa
    VisualizandoQuadro --> ModalTarefa : Clica em Nova/Editar Tarefa
    ModalTarefa --> CondicaoSubmitTarefa : Submete
    CondicaoSubmitTarefa --> ModalTarefa : Falha
    CondicaoSubmitTarefa --> VisualizandoQuadro : Sucesso
    ModalTarefa --> VisualizandoQuadro : Clica em Cancelar

    VisualizandoQuadro --> ModalDeletarTarefa : Clica em Excluir Tarefa
    ModalDeletarTarefa --> VisualizandoQuadro : Confirma ou Cancela

    %% Fluxo Visual de Drag and Drop
    VisualizandoQuadro --> ArrastandoCartao : Segura o Cartão (Drag Start)
    ArrastandoCartao --> CondicaoDrop : Solta o Cartão (Drop)
    CondicaoDrop --> VisualizandoQuadro : Zona Válida (Dispara PUT para API)
    CondicaoDrop --> VisualizandoQuadro : Zona Inválida (Reverte Animação)
  }
