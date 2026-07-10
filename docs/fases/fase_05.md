# 🏁 Fase 05: Integração Contínua e Automação de Testes (CI)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** GitHub Actions, Ruff (Linter analítico rápido) e Docker Multi-Stage Builds com diretivas `USER nonroot`.
* **O que sai ou modifica:** A validação manual de testes pelo desenvolvedor é descontinuada. O Dockerfile básico é substituído por uma estrutura de múltiplos estágios de build.
* **Habilidades e Conceitos Explorados:** Automação de pipelines de integração baseados em eventos do Git, otimização e enxugamento de camadas de imagens Docker e aplicação do princípio de menor privilégio em segurança de containers.
* **Justificativa Arquitetural:** Garantir de forma automatizada na nuvem que nenhum código quebre os critérios de aceitação do TDD antes de chegar na branch principal. A refatoração do Dockerfile quita o débito técnico da Fase 03, isolando ferramentas de build (compiladores) do artefato final de runtime, reduzindo o tamanho da imagem e removendo o usuário `root` da execução para blindar a segurança.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Estabilizar. Como o core de execução lágica e banco não mudou, a pegada de RAM da aplicação rodando no Home Server permanecerá idêntica à da fase anterior.
* **Impacto em CPU & Concorrência:** Estabilizar no servidor; consumo intensivo temporário nos runners em nuvem do GitHub Actions durante o processo de linting, testes com Pytest e compilação de camadas do Docker.
* **Impacto em Latência:** Desempenho idêntico. A otimização do tamanho das imagens impacta a velocidade do armazenamento em disco e transferência de rede, não alterando a velocidade de execução das rotas da API.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Runners virtuais do GitHub (Pipeline) e Home Server (Instância estável).
* **Mecanismo de Inicialização:** Automação via arquivo `.github/workflows/ci.yml` disparada automaticamente a cada `git push` ou Pull Request.
* **Snapshot Git:** `git switch -c snapshot/fase-05`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-05-integração-contínua)
