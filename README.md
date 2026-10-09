# Chama Ai Comanda

Modernizacao do sistema de restaurante para uma arquitetura offline-first:

- `desktop/`: Tauri v2, Vue 3 e TypeScript para o PDV Windows.
- `api/`: Laravel 12 em PHP 8.3 para pedidos, administracao e integracoes.
- `legacy/`: sistema CodeIgniter 2 preservado somente como referencia de migracao.
- `docker/`: imagens PHP-FPM e configuracao nginx.

## Ambiente local

Requisitos: Docker Desktop e Node.js 22+.

```powershell
docker compose up -d --build
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate
```

A API fica em `http://localhost:8080`. O MySQL local fica exposto em `localhost:3307`.

Para executar a interface web do PDV:

```powershell
npm --prefix desktop install
npm --prefix desktop run dev
```

Para gerar o aplicativo Tauri, instale Rust e os Build Tools C++ do Visual Studio e execute:

```powershell
npm --prefix desktop run tauri build
```

## Producao

Copie `.env.production.example` para `.env.production`, troque todos os segredos e execute:

```bash
docker compose -f docker-compose.prod.yml up -d --build
docker compose -f docker-compose.prod.yml exec app php artisan migrate --force
```

O workflow `deploy.yml` usa as variaveis `DEPLOY_HOST`, `DEPLOY_PORT`, `DEPLOY_USER`, `DEPLOY_PATH` e o secret `SSH_PRIVATE_KEY`. Sem `DEPLOY_HOST`, o deploy remoto e ignorado de forma segura.
