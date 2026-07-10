# 🏁 Fase 04: Deploy On-Premises e Roteamento (Home Server & Nginx)

## 🛠️ 1. Mudanças de Tecnologia e Escopo

* **O que entra:** Ubuntu Server, Nginx (Proxy Reverso) e firewall UFW.
* **O que sai ou modifica:** Os containers deixam de rodar no PC cliente e passam a operar de forma dedicada no hardware residencial. O acesso direto via porta do container é bloqueado; o Nginx intercepta tudo na porta HTTP 80.
* **Habilidades e Conceitos Explorados:** Administração de servidores Linux bare-metal, configuração de segurança periférica com UFW, proxy reverso, manipulação de cabeçalhos HTTP e roteamento de redes locais (LAN).
* **Justificativa Arquitetural:** Descarregar a aplicação no servidor dedicado residencial libera a máquina cliente e cria um ambiente real de staging. O Nginx blinda os containers da exposição direta à rede local e gerencia buffers de conexões, enquanto o UFW impede acessos não autorizados em portas administrativas (como a 5432 do Postgres).

## 🧪 2. Hipóteses de Engenharia & Resultados Esperados

* **Impacto em Memória RAM:** Redução drástica no cliente; estabilização eficiente no servidor. No Linux Ubuntu Server, os containers rodam nativamente sobre o Kernel (sem a VM do WSL2). A arquitetura orientada a eventos do Nginx consome uma pegada desprezível de RAM (<10MB).
* **Impacto em CPU & Concorrência:** Ganho de eficiência bruta. Sem hypervisors traduzindo Syscalls, o processador do servidor lidará melhor com a concorrência. O Nginx mitigará a carga do FastAPI digerindo requisições malformadas na borda.
* **Impacto em Latência:** Incremento na latência de rede externa (`front_click_to_request`) devido ao salto físico real da rede LAN (cabo/Wi-Fi do roteador). A comunicação interna entre a API e o banco permanece isolada na velocidade máxima da bridge do servidor.

## 📦 3. Produto Final Entregável

* **Ambiente de Execução:** Ambiente Servidor Dedicado (Intel Core i7-2600 / Linux Ubuntu Server).
* **Mecanismo de Inicialização:** Servidor operando com IP fixo na rede local, regras de UFW ativas (portas 22 e 80) e execução do `docker compose up -d` dentro do servidor.
* **Snapshot Git:** `git switch -c snapshot/fase-04`

---

### 🔗 Links Úteis desta Fase

* [Verificar o Contrato de Telemetria e Logs Utilizados](../especificacoes_logs.md)
* [Visualizar os Resultados de Performance Coletados desta Fase](../analise_resultados.md#-resultados-da-fase-04-deploy-on-premises)
