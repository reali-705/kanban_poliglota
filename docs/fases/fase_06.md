# 🏁 Fase 06: Entrega e Deploy Contínuo (CD) com Runners Isolados

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** GitHub Actions Self-Hosted Runner (instalado no servidor residencial) e GitHub Container Registry (GHCR).
* **O que sai ou modifica:** Eliminação completa da necessidade de intervenção manual via SSH, comandos manuais de `git pull` ou reciclagem de containers digitadas no terminal do servidor.
* **Habilidades e Conceitos Explorados:** Automação de esteiras de CD seguras de dentro para fora da rede (*outbound updates*), gerenciamento de permissões em registros de imagens centralizados e automação de deamons de sistema (systemd).
* **Justificativa Arquitetural:** Abrir portas públicas no roteador residencial (como a porta 22 de SSH) para a nuvem injeta um risco severo de segurança. O Self-Hosted Runner resolve isso estabelecendo conexões persistentes seguras de saída via WebSockets com o GitHub. O agente escuta a esteira, baixa de forma autônoma a imagem atualizada do GHCR e reinicia os serviços localmente, mantendo o firewall do servidor impenetrável.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Pequeno acréscimo estável em background no servidor. O agente do runner rodará como um daemon fixo no Ubuntu Server, consumindo entre 30MB e 50MB estáticos de RAM.
* **Impacto em CPU & Concorrência:** Pico severo e temporário de CPU no servidor residencial unicamente durante a execução do gatilho de deploy (extração de camadas e reciclagem do Docker Compose). Em repouso, o consumo do runner é de 0%.
* **Impacto em Latência:** Desempenho idêntico em uso normal. O indicador crítico afetado aqui não é a latência do K6 de ponta a ponta, mas sim o tempo da janela de indisponibilidade parcial (*downtime*) do sistema no momento exato em que o container antigo é desligado para a subida do novo.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Ambiente Servidor Dedicado (Intel Core i7-2600) executando o processo do runner local conectado ao GitHub.
* **Mecanismo de Inicialização:** Workflow configurado em `.github/workflows/cd.yml` orquestrando o deploy automatizado após o sucesso do CI.
* **Snapshot Git:** `git switch -c snapshot/fase-06`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-06-entrega-e-deploy-contínuo)
