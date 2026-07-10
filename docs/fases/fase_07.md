# 🏁 Fase 07: Desacoplamento e Expansão Poliglota (React, Go & Rust)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** TypeScript, React, Vite, Go (Gin/Fiber), gRPC (Protocol Buffers sobre HTTP/2), Redis, Rust (Cargo) e PyO3 para bindings de memória.
* **O que sai ou modifica:** A engine de visualização em Jinja2 + HTMX é totalmente depreciada. Rotas críticas de alta concorrência e processamento em lote são extraídas do FastAPI e migradas para Go. Algoritmos intensivos de ordenação lógica passam a rodar via extensão compilada nativa em Rust embutida no Python.
* **Habilidades e Conceitos Explorados:** Arquitetura de microsserviços distribuídos, comunicação RPC binária de alta performance, manipulação de caches em memória com Redis, concorrência real paralela e desenvolvimento de extensões de baixo nível integrando linguagens de sistemas (Rust) com linguagens de script (Python).
* **Justificativa Arquitetural:** Escalar processos concorrentes em Python esbarra no gargalo limitante do GIL. Transferir a renderização do front para o browser do cliente via React alivia o servidor. O microsserviço em Go lida com fluxos concorrentes massivos utilizando Goroutines leves multicore e gRPC binário sobre HTTP/2. O Rust entra cirurgicamente no core via PyO3 para que funções matemáticas e algorítmicas complexas rodem em velocidade de código de máquina puro com segurança total de memória.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Aumento global no servidor. Sustentar múltiplos ambientes de runtime separados (Python, Go, Redis, Nginx de arquivos estáticos) elevará a linha de base de RAM ociosa. Contudo, o consumo por requisição concorrente despencará drasticamente.
* **Impacto em CPU & Concorrência:** Alocação inteligente e otimizada dos núcleos físicos do processador i7-2600. O scheduler de Goroutines do Go explorará o paralelismo real do hardware, e as chamadas delegadas ao Rust rodarão fora do GIL do Python, reduzindo o desperdício de ciclos de processamento.
* **Impacto em Latência:** Redução drástica na latência de processamento interno; penalização milimétrica de rede. A quebra monolítica cobra um "imposto arquitetural": o salto de rede interna distribuída (*network hop*) entre os containers adicionará microsegundos, mas a velocidade de resposta do ecossistema Go/Redis compensará esse custo entregando respostas globais muito mais rápidas sob estresse.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Cliente (Browser executando a SPA React) e Servidor (Malha distribuída de containers políglotas interconectados).
* **Mecanismo de Inicialização:** Manifesto completo de containers interligados inicializado via `docker compose up --build -d`.
* **Snapshot Git:** `git switch -c snapshot/fase-07`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-07-desacoplamento-e-expansão)
