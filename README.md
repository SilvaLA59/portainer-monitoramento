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

**Via Portainer (Opção Manual - Web Editor - APENAS SWARM):**
Se o seu Portainer estiver rodando em um ambiente **Swarm** (menu "Configs" visível), e você preferir copiar e colar o `docker-compose.yml` diretamente no editor, siga estes passos para criar as Configs manualmente antes:

1.  No menu lateral esquerdo, vá em **Configs** (só aparece se o ambiente for Swarm).
2.  Clique em **Add config**.
3.  **Config 1 (Loki):**
    *   Name: `loki_config`
    *   Copie e cole o conteúdo do arquivo `loki-config.yml`.
    *   Clique em **Create config**.
4.  **Config 2 (Promtail):**
    *   Name: `promtail_config`
    *   Copie e cole o conteúdo do arquivo `promtail-config.yml`.
    *   Clique em **Create config**.
5.  Agora, ao criar a Stack no Web Editor, você precisará editar o `docker-compose.yml` para indicar que essas configs já existem externamente:

    ```yaml
    configs:
      loki_config:
        external: true
      promtail_config:
        external: true
    ```

**Nota para Ambientes Não-Swarm (Standalone):**
Se você não vê o menu "Configs" no Portainer, significa que seu ambiente é **Standalone**. Nesse caso, você **DEVE usar a opção "Repository" (Git)** descrita acima. O "Web Editor" não funcionará porque ele não conseguirá encontrar os arquivos `loki-config.yml` e `promtail-config.yml` se você apenas colar o YAML.

## Notas sobre a Configuração
- A rede `observability_net` está configurada para usar o driver padrão. No Swarm, ela será `overlay`. Localmente, será `bridge`.
- Os volumes são persistentes para garantir que os dados do Grafana e Loki não sejam perdidos ao reiniciar.
