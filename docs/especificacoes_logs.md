# 📊 Especificações de Telemetria, Logs e Benchmarks (K6)

Este documento estabelece as diretrizes de observabilidade, o funcionamento prático dos testes de carga e o contrato de dados de logs que todas as implementações de backend devem respeitar para garantir a validade científica do laboratório.

---

## 📝 1. O Contrato do Log Estruturado (Schema JSON)

Para eliminar pontos cegos de latência, o tráfego de dados é auditado de forma granular. O backend deve interceptar a requisição, capturar o `trace_id` gerado no frontend (ou injetá-lo caso não exista) e despejar no `stdout` um log estruturado em formato JSON plano com a seguinte anatomia de chaves:

```json
{
  "timestamp": "ISO-8601",
  "fase": "XX",
  "trace_id": "uuid-v4-de-correlacao-de-rede",
  "gargalos_ms": {
    "front_click_to_request": 0.0,
    "api_schema_validation": 0.0,
    "api_db_access_query": 0.0,
    "api_business_processing": 0.0,
    "front_receive_to_render": 0.0
  },
  "duracao_total_ciclo_ms": 0.0
}

```

### Dicionário de Chaves e Regras de Cálculo (`gargalos_ms`)

* **`front_click_to_request`**: Tempo decorrido (em milissegundos) desde o evento físico do clique ou arraste na tela até o envio real dos bytes na rede HTTP. Mede o overhead de processamento do cliente.
* **`api_schema_validation`**: Tempo gasto pelo framework (Pydantic/SQLModel no Python ou validadores do Go) descompactando o payload JSON e garantindo a tipagem dos dados.
* **`api_db_access_query`**: Tempo de espera bruto da chamada de persistência. Na Fase 01, mede o I/O em arquivo do SQLite; na Fase 02 em diante, mede o tempo de rede TCP do socket de ida e volta ao PostgreSQL.
* **`api_business_processing`**: Tempo dedicado estritamente à execução das regras de negócio do Kanban (casos de uso da Clean Architecture), filtros e ordenações lógicas.
* **`front_receive_to_render`**: Tempo que a interface gasta recebendo a resposta da API e processando o DOM (seja reinjetando HTML via HTMX ou remontando componentes com a reconciliação do React).

### 🌐 Origem das Métricas do Frontend

As chaves `front_click_to_request` e `front_receive_to_render` não são calculadas pelas ferramentas de teste de carga, mas sim instrumentadas diretamente no código do cliente através da **Performance Web API** do navegador, sendo propagadas via cabeçalhos HTTP customizados (`X-Front-Timestamp`) para o backend consolidar no log estruturado JSON.

---

## 🧪 2. Estrutura dos Cenários de Estresse (K6)

Os testes automatizados do K6 operam como a constante invariável do experimento. O script em JavaScript deve reproduzir o fluxo atômico completo: **Criar Tarefa ➔ Listar Quadro ➔ Mover Tarefa ➔ Deletar Tarefa**.

> ⚠️ **Regra de Isolamento de Concorrência (Multi-Tenant):** Para evitar contenção física de linhas (*row locking*) e falsos erros de travamento de banco de dados (`database is locked`), o script do K6 não deve disparar requisições estáticas contra um único ID de Quadro. Cada Usuário Virtual (VU) deve gerar dinamicamente um UUID único ou incrementar uma faixa de IDs exclusivos no bloco `setup()` para isolar seus dados durante o estresse.

Os quatro cenários abaixo devem ser aplicados de forma **idêntica** contra cada um dos entregáveis finais das fases:

### Cenário A: Vazão Máxima e Ponto de Ruptura (Throughput/RPS)

* **Objetivo:** Identificar o limite máximo de requisições por segundo (RPS) que a arquitetura suporta antes de começar a enfileirar requisições ou estourar a taxa de erros HTTP.
* **Configuração de Carga (Ramping VUs):**
  * 0 a 50 usuários simultâneos em 1 minuto.
  * 50 a 150 usuários simultâneos em 2 minutos.
  * 150 a 300 usuários simultâneos em 1 minuto.
* **Critério de Sucesso (SLA):** `http_req_failed` menor que 1% e latência p95 menor que 200ms.

### Cenário B: Saturação do Pool de Conexões

* **Objetivo:** Avaliar o comportamento do backend sob contenção de recursos de persistência. Configura-se o pool de conexões do SQLAlchemy/SQLModel para um limite fixo (ex: máximo de 20 conexões concorrentes) e joga-se uma carga massiva de VUs.
* **Configuração de Carga:** 100 usuários simultâneos (VUs) constantes injetados instantaneamente, sustentados por 3 minutos.
* **Métrica Alvo:** Monitorar a explosão da chave `api_db_access_query` nos logs. Se a latência de banco subir enquanto a CPU do PostgreSQL estiver baixa, o gargalo está no tamanho ou eficiência do pool.

### Cenário C: Teste de Pico Repentino (Spike Testing)

* **Objetivo:** Avaliar a resiliência das camadas de rede (Loopback do S.O., WSL2, Proxy Reverso do Nginx ou CNI do Kubernetes) ao lidar com aberturas em massa de sockets TCP de forma violenta.
* **Configuração de Carga:**
  * 0 usuários (Repouso/Idle).
  * Salto abrupto para 300 usuários simultâneos em 2 segundos, mantendo o pico por 30 segundos.
  * Queda instantânea de volta para 0 usuários.
* **Métrica Alvo:** Checar se ocorrem erros de conexão recusada ou *File Descriptors* esgotados no sistema hospedeiro.

### Cenário D: Teste de Resistência e Vazamento (Soak Testing)

* **Objetivo:** Identificar degradação contínua de hardware ao longo do tempo. Focado em caçar *Memory Leaks* (vazamentos de memória RAM) causados por sessões de banco não fechadas ou objetos órfãos no runtime.
* **Configuração de Carga:** 30 usuários simultâneos constantes por um período contínuo de 30 minutos.
* **Métrica Alvo:** O gráfico de consumo de memória RAM do processo/container da aplicação deve apresentar uma linha horizontal estabilizada. Caso apresente uma diagonal ascendente infinita, há vazamento ativo.

### Cenário E: Validadores Estressados (Payloads Inválidos)

* **Objetivo:** Mensurar o custo computacional que a camada de validação de tipos (`api_schema_validation`) cobra da CPU para processar e rejeitar requisições malformadas.
* **Configuração de Carga:** 50 usuários simultâneos constantes por 1 minuto enviando intencionalmente payloads corrompidos (Ex: títulos com mais de 300 caracteres ou tipos de dados invertidos).
* **Critério de Sucesso:** O sistema deve rejeitar 100% das requisições com código `HTTP 400 Bad Request` sem estourar o uso de memória RAM.

---

## 💻 3. Consolidação de Métricas de Infraestrutura

Além dos indicadores do K6, o analista deve documentar o impacto de hardware externo coletado pelos comandos do sistema operacional durante a execução dos testes:

* **RAM Estática/Ociosa (*Idle*):** Megabytes consumidos pela aplicação e bancos ativos sem nenhuma requisição trafegando.

* **RAM de Pico:** Consumo máximo de memória atingido durante os testes de estresse (Cenário A e C).

* **Densidade do Artefato:** Peso físico real em disco do entregável (Binário standalone compilado, peso da imagem do container Docker ou o tamanho total dos Pods no K3s).

---

## 🎭 4. Cenário de Experiência Visual do Usuário (Playwright)

Enquanto o K6 avalia a sustentabilidade do protocolo e do servidor sob volume massivo, a experiência visual de renderização na tela é auditada de forma isolada via **Playwright**.

* **Ferramenta Utilizada:** Playwright (Executando em Node.js sobre a máquina cliente).
* **Configuração de Carga:** 1 Usuário Virtual único operando um navegador real (Chromium).
* **Fluxo do Teste:** O robô abre a interface, clica no botão de criar tarefa, insere os dados e cronometra internamente o tempo decorrido desde o gatilho físico até o elemento final ser desenhado no DOM.
* **Métrica Extraída:** Latência Total Percebida pelo Usuário (Macro).
