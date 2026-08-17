# 🎮 Gaming DataForge API

A provider-driven real-time gaming & esports API for matches, teams, players, standings, statistics and news.

## Architecture

```text
Licensed/Public Data Provider
          ↓
Provider Adapter
          ↓
Polling / Sync Service
          ↓
PostgreSQL
          ↓
REST API + Socket.IO
```

## Important

This project does **not** invent live data. Configure a permitted provider in `.env`.

### Provider configuration

```env
PROVIDER_URL=https://your-permitted-provider.example
PROVIDER_KEY=your-key
POLL_SECONDS=30
```

The adapter expects these endpoints:

- `GET /tournaments/{slug}/standings`
- `GET /tournaments/{slug}/matches`

The provider response should contain a JSON object with a `data` array. Adapt `src/providers/httpProvider.ts` if your provider uses another schema.

## Run locally

```bash
npm install
cp .env.example .env
docker compose up -d db
npm run db:generate
npx prisma migrate dev --name init
npm run db:seed
npm run dev
```

API: `http://localhost:4000`

Health: `GET /health`

Leaderboard: `GET /api/v1/leaderboard?tournament=ewc-2026`

## Real-time events

Socket.IO events:

- `leaderboard:update`
- `match:update`
- `news:new`

## Security

Never commit `.env` or real API keys. Only commit `.env.example`.
