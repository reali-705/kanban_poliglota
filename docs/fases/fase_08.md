# 🏁 Fase 08: Orquestração Avançada e Observabilidade Coletiva (K3s)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** K3s (Kubernetes minimalista), Prometheus (Banco de séries temporais) e Grafana (Dashboards).
* **O que sai ou modifica:** O gerenciador Docker Compose é totalmente descontinuado. Toda a infraestrutura passa a ser declarada e mantida através de Manifestos lógicos do Kubernetes.
* **Habilidades e Conceitos Explorados:** Engenharia de confiabilidade de clusters (SRE), escrita de manifestos de orquestração (Deployments, Services, ConfigMaps), estratégias de deploy sem queda (*Rolling Updates*), raspagem automatizada de métricas de software (*metrics scraping*) e construção de dashboards de telemetria industrial.
* **Justificativa Arquitetural:** O Docker Compose atinge seu limite de sustentabilidade e resiliência ao gerenciar microsserviços políglotas. O K3s entra para prover recursos nativos de mercado como auto-cura (*self-healing* de pods caídos) e balanceamento de carga inteligente, quitando o débito técnico de downtime no deploy da Fase 06. Prometheus e Grafana unificam a observabilidade, cruzando os logs das aplicações com a telemetria do hardware de forma visual e perene.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Aumento massivo de linha de base estável fixada no servidor. O Control Plane do K3s rodando em background somado ao banco de séries temporais do Prometheus gerará um "imposto de infraestrutura" severo, consumindo de 500MB a 1GB de RAM permanente mesmo com o sistema ocioso.
* **Impacto em CPU & Concorrência:** Incremento contínuo e sutil no processamento básico em segundo plano, gerado pelos loops agendados de varredura (*scraping*) que o Prometheus executará a cada poucos segundos nos endpoints `/metrics` das aplicações.
* **Impacto em Latência:** Estabilizar com comportamento idêntico à Fase 07. A malha de rede interna gerenciada por CNIs leves do K3s (Flannel) gerencia o tráfego interno de pacotes de forma otimizada através de tabelas dinâmicas do kernel, garantindo que o ecossistema distribuído opere com alta eficiência de rede.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Nó mestre único Kubernetes ativo de forma perene no Servidor Dedicado (Intel i7-2600).
* **Mecanismo de Inicialização:** Aplicação em lote dos manifestos estruturados de dentro da pasta de configurações do cluster: `kubectl apply -f k8s/`.
* **Snapshot Git:** `git switch -c snapshot/fase-08`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-08-orquestração-avançada-e-observabilidade-coletiva)
