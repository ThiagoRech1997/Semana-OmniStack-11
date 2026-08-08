# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Be The Hero** — Semana OmniStack 11 project. A platform connecting NGOs (ONGs) with people willing to help by funding specific incidents/cases. Built with Express + Knex + SQLite (backend) and React + React Router v5 (frontend).

## Commands

### Backend (port 3333)

```bash
cd backend
npm install
npm run start          # nodemon dev server on :3333
npx knex migrate:latest --knexfile knexfile.js   # run migrations
```

### Frontend (port 3000)

```bash
cd frontend
npm install
npm run start          # CRA dev server on :3000
npm test               # jest via react-scripts
npm run build          # production build
```

## Architecture

### Backend (`backend/`)

Simple Express CRUD API with Knex.js query builder and SQLite3.

**Entities:**
- **ongs** — id (random 4-byte hex string), name, email, whatsapp, city, uf (2-char state)
- **incidents** — id (auto-increment), title, description, value (decimal), ong_id (FK → ongs.id)

**Routes** (`src/routes.js`):
- `POST /sessions` — authenticate ONG by id, returns ONG name
- `GET /ongs`, `POST /ongs` — list/create NGOs
- `GET /incidents` — paginated (5/page via `?page=N`), returns `X-Total-Count` header, joins ONG data
- `POST /incidents`, `DELETE /incidents/:id` — create/delete (ONG ownership check)
- `GET /profile` — list incidents for authenticated ONG

**Auth model:** ONG ID passed in `Authorization` request header (no JWT/token).

**Database:** SQLite at `backend/src/database/db.sqlite`. Config in `knexfile.js`. Migrations in `src/database/migrations/`.

### Frontend (`frontend/`)

Create React App with React Router v5 (BrowserRouter).

**Pages** (`src/pages/`):
- **Logon** (`/`) — login by ONG ID, stores `ongId`/`ongName` in localStorage
- **Register** (`/register`) — ONG registration form
- **Profile** (`/profile`) — incident dashboard with delete capability
- **NewIncident** (`/incidents/new`) — create incident form

**API client:** Axios instance at `src/services/api.js` pointing to `http://localhost:3333`.

Currency values formatted as BRL using `Intl.NumberFormat`.
