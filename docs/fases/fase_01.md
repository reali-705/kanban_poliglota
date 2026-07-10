# 🏁 Fase 01: Monolito Portátil Local (Full Python)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** Python 3.12, FastAPI, SQLModel, Jinja2, HTMX, PyInstaller/Nuitka e SQLite.
* **O que sai ou modifica:** Substituição total do repositório legado antigo. O desenvolvimento inicia do zero isolado nesta arquitetura.
* **Habilidades e Conceitos Explorados:** Desenvolvimento orientado a testes (TDD), padrões de Arquitetura Limpa, manipulação de hipertexto assíncrono com HTMX sem a necessidade de construir uma SPA complexa e compilação de runtimes interpretadas para executáveis nativos (*standalone*).
* **Justificativa Arquitetural:** Unificar o front e o back via Jinja2 + HTMX elimina o overhead de rede e a complexidade de rotas de uma aplicação distribuída neste momento inicial. O SQLite embarcado mantém o tráfego de dados local e permite empacotar todo o sistema em um único binário portátil distribuível.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Estabilizar (Baixo consumo). O SQLite opera em processo (I/O direto no arquivo local), eliminando o consumo de um SGBD separado rodando em background. A pegada de RAM ficará restrita ao runtime do FastAPI (~40MB - 60MB).
* **Impacto em CPU & Concorrência:** Overhead concentrado no interpretador. Devido ao GIL (*Global Interpreter Lock*) do Python e ao bloqueio físico de escrita do SQLite (*database is locked*), o sistema atingirá picos de CPU rapidamente sob estresse, limitando a concorrência massiva.
* **Impacto em Latência:** Resposta ultra-rápida (Sub-milissegundo no banco). A renderização ocorrendo no servidor (SSR) combinada com o banco em arquivo local elimina o *network round-trip* da rede física.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Ambiente Cliente (PC Principal com Windows 11 / AMD Ryzen 5 8600G).
* **Mecanismo de Inicialização:** Execução direta do executável compilado (.exe) via PowerShell ou comando `uv run src/kanban/main.py`.
* **Snapshot Git:** `git switch -c snapshot/fase-01`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-01-monolito-python)
