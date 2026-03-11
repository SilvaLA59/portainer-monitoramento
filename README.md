# Stack de Monitoramento (Logging)

Esta stack configura o **Grafana**, **Loki** e **Promtail** para agregação e visualização de logs de containers Docker.

## Serviços
- **Grafana**: Visualização dos dados (Porta 3000).
- **Loki**: Sistema de agregação de logs.
- **Promtail**: Agente que coleta logs dos containers e envia para o Loki.

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
