# Stack de Monitoramento (Logging)

## Objetivo
Esta stack oferece uma solução completa e leve para **Observabilidade de Logs** em ambientes Docker. Seu principal objetivo é centralizar os logs de todos os containers (seja em um único host ou em um cluster Swarm), permitindo pesquisa, filtragem e visualização em tempo real sem a necessidade de acessar individualmente cada servidor ou container.

## Componentes da Stack

### 1. Grafana
**Interface de Visualização e Análise.**
O Grafana é o painel de controle onde você visualiza os dados. Nesta stack, ele já vem pré-configurado com o Loki como fonte de dados (datasource), permitindo que você use a aba "Explore" para consultar logs usando a linguagem LogQL.
- **Porta:** 3000 (Acesso Web)
- **Função:** Query e Visualização de Logs.

### 2. Loki
**Armazenamento e Indexação de Logs.**
O Loki é um sistema de agregação de logs inspirado no Prometheus. Diferente de outras soluções (como ELK), ele não indexa o texto completo dos logs, mas apenas os metadados (labels), o que o torna extremamente leve e eficiente em termos de armazenamento e memória.
- **Porta:** 3100 (Acesso interno via API)
- **Função:** Receber, armazenar e servir logs para o Grafana.

### 3. Promtail
**Agente Coletor de Logs.**
O Promtail é o agente que roda em cada nó do Docker. Ele monitora os arquivos de log locais dos containers, anexa labels úteis (como `container_name`, `service_name`, `stack_name`) e envia os logs para o Loki.
- **Modo de Deploy:** Global (roda uma instância em cada node do Swarm) ou Local.
- **Função:** Coleta (tailing), processamento (labeling) e envio (shipping) de logs.

## Pré-requisitos
- Docker e Docker Compose (para execução local).
- Docker Swarm (para execução em cluster).

## Como rodar

### 1. Local (Docker Compose)
Para rodar a stack localmente em sua máquina:

```bash
docker compose up -d
```

Acesse o Grafana em: `http://localhost:3000`
- Usuário: `admin`
- Senha: `ChangeStrongPassword` (definida no docker-compose.yml)

### 2. Docker Swarm (Portainer ou CLI)
Para rodar em um cluster Swarm:

**Via CLI:**
```bash
docker stack deploy -c docker-compose.yml monitoramento
```

**Via Portainer (Opção Recomendada - Git):**
1. Crie uma nova **Stack**.
2. Selecione a opção **Repository**.
3. Conecte ao seu repositório Git onde este arquivo está.
4. O Portainer irá baixar os arquivos e criar os Configs e Serviços automaticamente.

**Via Portainer (Opção Manual - Web Editor - SWARM):**
Como este arquivo `docker-compose.yml` agora é **Self-Contained** (contém todas as configurações embutidas), você pode copiar e colar o conteúdo diretamente no Web Editor do Portainer sem precisar criar configs manualmente antes.

1. Crie uma nova **Stack**.
2. Selecione **Web Editor**.
3. Cole o conteúdo do `docker-compose.yml`.
4. Faça o Deploy.

**Nota:** A opção **Repository (Git)** continua sendo a mais recomendada para manter o versionamento e atualizações automáticas.

## Solução de Problemas Comuns

### Erro: "bind source path does not exist"
Isso ocorre quando você tenta usar o **Web Editor** do Portainer e cola o YAML, mas o Portainer não tem acesso aos arquivos de configuração locais.
**Solução:** Use a opção **Repository (Git)** para fazer o deploy da stack.

### Erro: "service ... uses an undefined config"
Ocorre se você definiu uma config como `external: true` mas esqueceu de criá-la no menu "Configs" antes do deploy.

### Erro: "invalid mount config for type 'bind'"
Geralmente causado por caminhos de arquivo incorretos. A stack atual usa caminhos relativos (`./arquivo.yml`) que funcionam perfeitamente quando o deploy é feito via **Git** ou **Docker Compose local**.

## Notas sobre a Configuração
- A rede `observability_net` está configurada para usar o driver padrão. No Swarm, ela será `overlay`. Localmente, será `bridge`.
- Os volumes são persistentes para garantir que os dados do Grafana e Loki não sejam perdidos ao reiniciar.
