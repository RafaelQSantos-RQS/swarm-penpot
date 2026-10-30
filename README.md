# Swarm Penpot

Deploy do [Penpot](https://penpot.app/) para **Docker Swarm**, integrado ao **Traefik** através da rede externa `swarm-net`.

---

## 📋 Pré-requisitos

1. **Docker Swarm Ativo**:
   ```bash
   docker swarm init
   ```

2. **Rede Externa `swarm-net`**:
   Caso ainda não exista, crie a rede overlay compartilhada com o Traefik:
   ```bash
   docker network create --driver overlay --attachable swarm-net
   ```

---

## 🚀 Deploy em um único comando

Execute o comando abaixo substituindo `penpot.seudominio.com` pelo seu domínio real:

```bash
STACK_NAME=penpot PROXY_URL=penpot.seudominio.com docker stack deploy -c docker-stack.yml penpot
```

> [!NOTE]
> * **`STACK_NAME`**: define o nome da stack no Swarm e o identificador dos roteadores/serviços no Traefik.
> * **`PROXY_URL`**: define o domínio principal do Penpot acessível via HTTPS e alinha o `PENPOT_PUBLIC_URI`.

---

## 🌐 Endpoints de Acesso

Após a convergência dos serviços:

* **Penpot Web**: `https://${PROXY_URL}` (ex: `https://penpot.seudominio.com`)
* **Mailpit (Webmail de Testes)**: `https://mailpit-${PROXY_URL}` (ex: `https://mailpit-penpot.seudominio.com`)

---

## 🛠️ Serviços Inclusos na Stack

| Serviço | Imagem | Descrição |
| :--- | :--- | :--- |
| `frontend` | `penpotapp/frontend:2.18` | Interface web (SPA) exposta via Traefik (porta 8080) |
| `backend` | `penpotapp/backend:2.18` | API central da aplicação (Clojure) |
| `admin-console`| `penpotapp/admin-console:2.18` | Painel de administração |
| `exporter` | `penpotapp/exporter:2.18` | Motor de exportação de arquivos (PDF/SVG/PNG) |
| `mcp` | `penpotapp/mcp:2.18` | Servidor MCP (integrações de IA) |
| `postgres` | `postgres:15` | Banco de dados PostgreSQL com volume local `PENPOT_POSTGRES` |
| `valkey` | `valkey/valkey:8.1` | Cache em memória e WebSockets |
| `mailpit` | `axllent/mailpit:v1` | Servidor SMTP local exposto via Traefik (porta 8025) |

---

## 📊 Comandos Úteis

* **Verificar status dos serviços:**
  ```bash
  docker stack services penpot
  ```

* **Verificar containers e nós de execução:**
  ```bash
  docker stack ps penpot
  ```

* **Visualizar logs de um serviço:**
  ```bash
  docker service logs -f penpot_backend
  ```

* **Remover a stack:**
  ```bash
  docker stack rm penpot
  ```
