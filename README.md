# 🎮 Gaming DataForge API

Provider-driven gaming & esports API for live news, teams, players, matches, statistics and tournament leaderboards.

## Live provider architecture

This repository **does not fake live data**. It uses a provider adapter so you can plug in a real public/licensed provider you are permitted to access.

```text
Real provider → HttpEsportsProvider → Sync service → PostgreSQL
                                           ↓
                                    REST + Socket.IO
```

### Configure

Copy `.env.example` to `.env` and set:

```env
EWC_PROVIDER_URL=https://your-permitted-provider.example
EWC_PROVIDER_KEY=your-key
EWC_POLL_SECONDS=15
```

The adapter expects:

- `GET /tournaments/ewc-2026/standings`
- `GET /tournaments/ewc-2026/matches`

with `{ "data": [...] }` JSON responses. If a provider has a different schema, change only `src/providers/httpProvider.ts` or add another adapter implementing `EsportsProvider`.

## REST endpoints

- `GET /health`
- `GET /api/v1/games`
- `GET /api/v1/teams?game=free-fire`
- `GET /api/v1/players`
- `GET /api/v1/tournaments`
- `GET /api/v1/matches?tournament=ewc-2026`
- `GET /api/v1/leaderboard?tournament=ewc-2026`
- `GET /api/v1/news`
- `POST /api/v1/sync/ewc`

Protected routes use `x-api-key`.

## Realtime events

Socket.IO events:

- `leaderboard:update`
- `match:update`
- `news:new`

When the provider reports new match results or standings, the sync service persists them and broadcasts updates.

## News

Set `NEWS_INGEST_ENABLED=true` and put permitted RSS feeds in `NEWS_FEEDS`. The service polls them and emits `news:new` for new items. Respect each feed's terms, attribution and reuse requirements.

## Run

```bash
npm install
cp .env.example .env
docker compose up -d db
npm run db:generate
npx prisma migrate dev --name init
npm run db:seed
npm run dev
```

## Important

A provider adapter is **not itself a data source**. You still need a real provider and permission/terms that allow the intended use. Do not put provider keys in GitHub; keep them in `.env` or deployment secrets.
