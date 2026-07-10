# 🏁 Fase 03: Conteinerização e Isolamento de Ambiente (Docker)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** Docker Engine, Docker Compose e arquivos de configuração `.env` protegidos.
* **O que sai ou modifica:** A execução nativa direta dos runtimes e serviços no S.O. hospedeiro é descontinuada. Tudo passa a rodar encapsulado em uma rede isolada virtual do Docker.
* **Habilidades e Conceitos Explorados:** Criação de receitas de build (*Dockerfiles*), orquestração de multi-containers, isolamento de redes virtuais e gerenciamento de persistência através de volumes virtuais.
* **Justificativa Arquitetural:** Eliminar o problema de incompatibilidade de ambientes ("funciona na minha máquina") e garantir a paridade absoluta entre a máquina de desenvolvimento e o servidor de deploy. Resolve o débito técnico da Fase 02 ao isolar credenciais em variáveis de ambiente confidenciais.

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Aumentar consideravelmente (no ambiente Windows). O Docker Desktop depende do subsistema WSL2, que instanciará uma VM leve do Linux para gerenciar o daemon de containers, gerando uma fatia de alocação de RAM fixa no Windows.
* **Impacto em CPU & Concorrência:** Pequeno overhead de processamento sob estresse extremo, decorrente da camada de tradução de chamadas de sistema (Syscalls) e roteamento de pacotes entre o host e o kernel do WSL2.
* **Impacto em Latência:** Estabilizar ou sofrer acréscimo milimétrico. A comunicação da API com o banco abandona o `127.0.0.1` e passa a trafegar via DNS interno da rede bridge do Docker, adicionando microsegundos no processamento dos pacotes lógicos.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Ambiente Cliente (Windows 11 / Docker Desktop sobre WSL2).
* **Mecanismo de Inicialização:** Comando único de orquestração: `docker compose --env-file .env up --build -d`.
* **Snapshot Git:** `git switch -c snapshot/fase-03`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-03-conteinerização)
