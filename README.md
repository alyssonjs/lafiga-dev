# Láfiga — ambiente de desenvolvimento (Docker)

Stack: **PostgreSQL**, **Redis**, **API Rails** (`../api`) e o **front Vite** no repositório **`lafiga-web`** (`../lafiga-web`).

## Repositório do front (`lafiga-web`)

O Docker monta **`../lafiga-web`** (submódulo Git ao lado de `api/` e desta pasta).

Ao clonar o monorepo `lafiga`, inicializa o submódulo:

```bash
git submodule update --init --recursive
```

(Alternativa manual: `git clone https://github.com/alyssonjs/lafiga-web.git ../lafiga-web` na raiz do monorepo.)

A pasta legada **`front-lafiga`** no mesmo nível **não** é usada por este compose; o código canónico do SPA é **`lafiga-web`**.

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

O browser chama a API em `http://localhost:3001` (`VITE_API_BASE_URL`). O CORS da API já inclui a origem do Vite (`5173`).

## Front antigo (Next.js)

O app em `../front` **não** é mais montado neste compose. Para voltar a usá-lo, restaure o bloco `front` anterior no histórico do Git (volume `../front`, porta `3000`, `NEXT_PUBLIC_API_BASE_URL`).

## Variáveis úteis (front)

Definidas no `docker-compose.yml` para o serviço `front`. Para ajustar localmente, copie `../lafiga-web/.env.example` para `.env` na pasta `lafiga-web`.

- `VITE_API_BASE_URL` — base da API (no host, não use o nome do container).
- `VITE_USE_MOCK_DATA` — `false` para usar a API nos catálogos integrados.
- `VITE_DEFAULT_PLAYER_ROLE_ID` — `role_id` do jogador no signup (alinhar ao seed Rails).

## `node_modules` no Docker

O volume nomeado `front_lafiga_node_modules` mantém dependências do Vite **dentro** do container, evitando misturar com o sistema de ficheiros do host (útil em macOS/ARM).
