# 🏁 Fase 02: Persistência de Produção e Migrações (PostgreSQL & Alembic)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** PostgreSQL 17.0, driver assíncrono `asyncpg` e Alembic para controle de esquemas.
* **O que sai ou modifica:** O mecanismo embarcado SQLite é completamente removido. A camada de infraestrutura de dados é reescrita para gerenciar sessões assíncronas.
* **Habilidades e Conceitos Explorados:** Isolamento transacional, gerenciamento de pools de conexões assíncronas, versionamento de banco de dados via código (*database migrations*) e tratamento concorrente de dados.
* **Justificativa Arquitetural:** O SQLite barra a evolução poliglota por não suportar acessos concorrentes distribuídos externos. A migração para o PostgreSQL desacopla o estado do dado da runtime do servidor. O Alembic entra para garantir a reprodutibilidade estrutural das tabelas no Git, eliminando alterações manuais *bare-metal*.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Aumentar. O PostgreSQL exige alocação de buffers e processos filhos separados no Sistema Operacional. A API FastAPI também consumirá mais RAM para sustentar o Pool de Conexões ativo em memória.
* **Impacto em CPU & Concorrência:** Redução de picos severos de CPU sob carga de escrita. O mecanismo MVCC do Postgres permite escritas e leituras simultâneas não-bloqueantes, otimizando o loop de eventos assíncronos do FastAPI.
* **Impacto em Latência:** Incremento inevitável na camada de dados (`api_db_access_query`). O transporte dos dados sai do barramento local e passa a trafegar via protocolo de rede TCP/IP (mesmo em loopback), injetando o custo de serialização de pacotes.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Ambiente Cliente (PC Principal se comunicando via loopback TCP).
* **Mecanismo de Inicialização:** Instância local do Postgres ativa na porta 5432, execução de `alembic upgrade head` e inicialização da API via `uv run`.
* **Snapshot Git:** `git switch -c snapshot/fase-02`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-02-persistência-de-produção-e-migrações)
