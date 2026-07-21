# 🔬 Relatório de Análises e Conclusões de Performance

Este documento centraliza as métricas empíricas coletadas pelo K6, capturas de tela dos dashboards e o confronto das hipóteses teóricas de cada fase evolutiva do projeto.

---

## 📊 Matriz Comparativa Consolidada (Linha de Base)

> Esta tabela será atualizada ao fim de cada fase para gerar um panorama visual imediato da evolução da eficiência do hardware.

| Métrica Analisada | Fase 01 (Monolito) | Fase 02 (Postgres) | Fase 03 (Docker) | Fase 04 (Server) | Fase 07 (Poliglota) | Fase 08 (K8s) |
| :-- | :-: | :-: | :-: | :-: | :-: | :-: |
| **Latência Backend Puro (K6 - ms)** | | | | | | |
| **Latência Total na Tela (Playwright - ms)** | | | | | | |
| **Overhead de Tela (Diferença - ms)** | | | | | | |
| **RAM em Idle (MB)** | | | | | | |
| **RAM em Pico (MB)** | | | | | | |
| **Pico de CPU (%)** | | | | | | |
| **Tamanho do Artefato (MB)** | | | | | | |
| **Downtime de Deploy (s)** | N/A | N/A | N/A | | | |

---

## 📜 Histórico e Conclusões por Fase

### Resultados da Fase 01: Monolito Portátil Local

* **Métricas Reais Coletadas (K6):** [Inserir dados brutos]
* **Confronto com a Hipótese:** [O comportamento teórico de RAM/CPU se confirmou? Explicar desvios]
* **Análise de Custo de Oportunidade:** [O ganho técnico justificou a simplicidade da stack?]
* **Evidências Visuais (Assets):** ![Gráfico de Carga Fase 1](assets/placeholder.png)
* **Diferença Estrutural de Interface (HTMX):** Registrar quantos milissegundos a interface gastou para embutir o HTML recebido no DOM (Métrica extraída subtraindo o tempo do K6 do tempo total do Playwright).

### Resultados da Fase 02: Persistência de Produção e Migrações

* **Métricas Reais Coletadas (K6):** [Inserir dados brutos]
* **Confronto com a Hipótese:** [Análise do impacto da introdução da rede TCP sobre a query do banco]
* **Análise de Custo de Oportunidade:** [O custo computacional do Postgres compensou a segurança dos dados?]

### Resultados da Fase 03: Conteinerização e Isolamento de Ambiente

### Resultados da Fase 04: Deploy On-Premises e Roteamento

### Resultados da Fase 05: Integração Contínua e Automação de Testes

### Resultados da Fase 06: Entrega e Deploy Contínuo com Runners Isolados

### Resultados da Fase 07: Desacoplamento e Expansão Poliglota

### Resultados da Fase 08: Orquestração Avançada e Observabilidade Coletiva
