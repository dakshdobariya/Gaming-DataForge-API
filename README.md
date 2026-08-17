# 🎮 GamePulse Live API

A real-time-oriented gaming & esports information API.

## What is actually live?

This project is designed so that **current data comes from a configured permitted/public/licensed provider**. It does NOT fake live data.

- 📰 News: periodic RSS ingestion when enabled.
- 🏆 EWC leaderboard: periodic provider sync when `EWC_PROVIDER_URL` is configured.
- 🎯 Match results: synced after provider reports them.
- ⚡ WebSocket: broadcasts `leaderboard:update`, `match:update`, and `news:new`.
- 📊 Rankings: API recalculates ordering from stored current stats.

## Important

There is no universal official public EWC API documented in this repository. Therefore the ZIP does not invent one. You must configure an actual provider that gives you permission to access the data.

The official EWC Resource Center provides official tournament resources and competitive operations information. Public tournament statistics can also be available from third-party esports databases, but their terms/API access must be respected.

## Endpoints

`GET /health`

`GET /api/v1/games`

`GET /api/v1/teams?game=free-fire`

`GET /api/v1/players`

`GET /api/v1/tournaments`

`GET /api/v1/matches?tournament=ewc-2026`

`GET /api/v1/leaderboard?tournament=ewc-2026`

`GET /api/v1/news?game=free-fire`

`POST /api/v1/sync/ewc`

Protected endpoints require `x-api-key`.

## Realtime events

Connect with Socket.IO and listen for:

- `leaderboard:update`
- `match:update`
- `news:new`

## Setup

1. `npm install`
2. Copy `.env.example` to `.env`
3. Start PostgreSQL:
   `docker compose up -d db`
4. `npm run db:generate`
5. `npx prisma migrate dev --name init`
6. `npm run db:seed`
7. `npm run dev`

## Live provider

Set:

`EWC_PROVIDER_URL=https://your-permitted-provider.example`

`EWC_PROVIDER_KEY=your-key`

The provider adapter expects:

`GET /tournaments/ewc-2026/standings`

and

`GET /tournaments/ewc-2026/matches`

with JSON shaped like:

`{ "data": [...] }`

If your provider uses a different schema, modify `src/providers/httpProvider.ts`.

## News

Set:

`NEWS_INGEST_ENABLED=true`

`NEWS_FEEDS=https://your-permitted-feed.xml`

Use only sources/feeds whose terms permit your intended API use and preserve source URLs/attribution.

## Docker

`docker compose up --build`

## No fake real-time data

The repository intentionally ships only a small demo structure. It will not claim a leaderboard is live until a real provider is configured and successfully synced.
