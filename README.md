# Láfiga — ambiente de desenvolvimento (Docker)

Stack: **PostgreSQL**, **Redis**, **API Rails** (`../api`) e o **front Vite** em **`../front-lafiga`** (montado pelo `docker-compose.yml` desta pasta).

## Repositório do front

O compose atual monta **`../front-lafiga`**. O submódulo **`lafiga-web`** pode apontar para o mesmo remoto; garante que editas a pasta que o volume aponta (ver comentário no `docker-compose.yml`).

Ao clonar o monorepo com submódulos:

```bash
git submodule update --init --recursive
```

## Subir tudo

Na raiz desta pasta:

```bash
docker compose up --build
```

| Serviço | URL |
|--------|-----|
| **Front (Vite)** | http://localhost:5173 |
| **API Rails** | http://localhost:3001 |
| **PostgreSQL** | `localhost:5432` (user `lafiga_api`, DB `lafiga-api_db_1`) |
| **Redis** | `localhost:6379` |

O browser chama a API em `http://localhost:3001` (`VITE_API_BASE_URL`). O CORS da API já inclui a origem do Vite (`5173`). O WebSocket do ActionCable usa **`ws://localhost:3001/cable`** (derivado no front a partir da mesma base); `config.action_cable.allowed_request_origins` em development já inclui `http://localhost:5173`.

## API: `db:seed` e opcionais

Com os containers a correr, na pasta **`../api`** (ou `docker compose exec api …`):

```bash
docker compose exec api bin/rails db:seed
```

Opcional (mesmo `exec`), ver `api/README.md`: `SEED_MONSTERS=1`,
`SEED_IMPORTED_SHEETS_REHYDRATE=1`, `SKIP_DND_API=1`.

## Front antigo (Next.js)

O app em `../front` **não** é mais montado neste compose. Para voltar a usá-lo, restaure o bloco `front` anterior no histórico do Git (volume `../front`, porta `3000`, `NEXT_PUBLIC_API_BASE_URL`).

## Variáveis úteis (front)

Definidas no `docker-compose.yml` para o serviço `front`. Para overrides sem alterar o compose, cria **`../front-lafiga/.env`** (só o Vite lê `VITE_*` daqui — **não** uses `api/.env` para isso). Reinicia o container `front` após mudar o `.env`.

- `VITE_API_BASE_URL` — base da API (no host, não use o nome do container).
- `VITE_SESSION_FEED_CABLE` — no compose está `true`: feed efémero da sessão (chat + dados) via **SessionFeedChannel** em tempo real entre browsers. Com `false`, o feed fica só em BroadcastChannel na mesma máquina.
- `VITE_USE_MOCK_DATA` — `false` para usar a API nos catálogos integrados.
- `VITE_DEFAULT_PLAYER_ROLE_ID` — `role_id` do jogador no signup (alinhar ao seed Rails).
- `VITE_GIPHY_API_KEY` / `VITE_TENOR_API_KEY` — busca de GIF no chat da sessão (opcional; ver `front-lafiga/.env.example`).

A API no compose recebe **`REDIS_URL`**; o throttle do session feed usa Redis em development quando essa variável existe (evita depender de `rails dev:cache` com `null_store`).

## `node_modules` no Docker

O volume nomeado `front_lafiga_node_modules` mantém dependências do Vite **dentro** do container, evitando misturar com o sistema de ficheiros do host (útil em macOS/ARM).
