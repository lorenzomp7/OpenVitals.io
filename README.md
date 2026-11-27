<p align="center">
  <h1 align="center">OpenVitals</h1>
</p>

<p align="center">
  The open-source platform for understanding your health data.
  <br />
  <a href="https://openvitals.io"><strong>openvitals.io</strong></a> · <a href="https://github.com/zmeyer44/OpenVitals/issues">Issues</a> · <a href="https://github.com/zmeyer44/OpenVitals">GitHub</a>
</p>

<p align="center">
  <a href="https://github.com/zmeyer44/OpenVitals/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-GPL--3.0-blue" alt="License" /></a>
</p>

<p align="center">
  <img src="https://openvitals.io/images/landing-page.png" alt="OpenVitals — Understand Your Health Data" width="100%" />
</p>

<p align="center">⭐ Star this repo to help others discover it</p>

---

OpenVitals parses, normalizes, and tracks health records from any lab, provider, or format. Upload a PDF from Quest Diagnostics, sync your Whoop, or import a CSV — AI extracts every value, maps it to standard codes, and lets you track trends across years of results. Every observation traces back to its source with full provenance, confidence scoring, and a complete audit trail.

Use the hosted version at [openvitals.io](https://openvitals.io), or self-host the entire platform on your own infrastructure.

## Features

- **AI-Powered Ingestion** — Upload lab PDFs, CSVs, images, or any medical document. AI parses and normalizes values, units, and codes automatically.
- **Full Provenance** — Every data point traces back to its source document, parser version, and confidence score. No black boxes.
- **AI Health Chat** — Ask questions about your health data and get answers grounded in your actual records, with citations to specific observations and dates.
- **Wearable Integration** — Connect Whoop (and more coming) to pull in recovery scores, HRV, sleep, and activity data alongside your lab work.
- **Scoped Sharing** — Create time-limited, category-filtered shares for your doctors. Your cardiologist sees lipids and vitals. Your nutritionist sees diet-related labs.
- **Medication Tracking** — Log medications and supplements, then correlate them against your biomarker trends over time.
- **Biomarker Dashboard** — Unified view of all health metrics with trend charts, optimal ranges, and status indicators.
- **13+ Data Categories** — Labs, vitals, medications, conditions, encounters, imaging, dental, immunizations, allergies, procedures, social history, family history, and wearable data.
- **Plugin SDK** — Extend with custom parsers, views, and analyzers.
- **Self-Hostable** — TypeScript end-to-end, Postgres, Drizzle ORM, tRPC. Run it yourself, read every line of code.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | [Next.js](https://nextjs.org) 16 (App Router, Turbopack) |
| Language | [TypeScript](https://www.typescriptlang.org) 5.9 |
| UI | [React](https://react.dev) 19, [Radix UI](https://www.radix-ui.com), [Tailwind CSS](https://tailwindcss.com) 4 |
| API | [tRPC](https://trpc.io) 11 (end-to-end type safety) |
| Database | [PostgreSQL](https://www.postgresql.org) 16, [Drizzle ORM](https://orm.drizzle.team) |
| Auth | [BetterAuth](https://www.better-auth.com) (email/password, Google OAuth) |
| AI | [Anthropic Claude](https://www.anthropic.com) via [Vercel AI SDK](https://sdk.vercel.ai) |
| Monorepo | [pnpm](https://pnpm.io) workspaces, [Turborepo](https://turbo.build) |
| File Storage | [Vercel Blob](https://vercel.com/docs/storage/vercel-blob) (production) / local filesystem (dev) |
| Charts | [Recharts](https://recharts.org) |
| E2E Tests | [Playwright](https://playwright.dev) |

## Project Structure

```
openvitals/
├── apps/
│   └── web/                     # Next.js frontend & API
│       ├── app/                 # App Router (auth, dashboard, marketing, onboarding)
│       ├── components/          # Shared React components
│       ├── features/            # Feature modules (AI chat, marketing, layout)
│       └── server/              # tRPC routers, auth config, integrations
├── packages/
│   ├── ai/                      # AI/Claude integration logic
│   ├── blob-storage/            # File storage abstraction
│   ├── common/                  # Shared types and utilities
│   ├── database/                # Drizzle schema, migrations, queries, seeds
│   ├── events/                  # Event system
│   ├── ingestion/               # Document parsing logic
│   ├── plugin-sdk/              # Plugin system SDK
│   └── sharing/                 # Data sharing utilities
└── services/
    └── ingestion-worker/        # Background worker for async document processing
```

## Self-Hosting Guide

This repository contains the web platform. The Android app is maintained separately in [OpenVitals](https://github.com/lorenzomp7/OpenVitals). The workspace pins pnpm 9.15.4 and requires Node.js 20 or later.

### Prerequisites

- [Node.js](https://nodejs.org) >= 20
- [pnpm](https://pnpm.io) 9.15+
- [Docker](https://www.docker.com) (for PostgreSQL, or bring your own Postgres 16+ instance)

### 1. Clone the repository

```bash
git clone https://github.com/lorenzomp7/OpenVitals.io.git
cd OpenVitals.io
```

### 2. Install dependencies

```bash
pnpm install
```

### 3. Start PostgreSQL

Using Docker Compose:

```bash
docker compose up -d
```

This starts a Postgres 16 instance on `localhost:5432` with database `openvitals`, user `postgres`, password `postgres`.

If you have your own Postgres instance, skip this step and set `DATABASE_URL` accordingly in the next step.

### 4. Configure environment variables

```bash
cp .env.example .env
```

The defaults in `.env.example` are configured for local development and will work out of the box with the Docker Compose database. Open `.env` and review:

```bash
# Database — matches docker-compose.yml defaults
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/openvitals

# Auth — change in production
BETTER_AUTH_SECRET=change-me-in-production
BETTER_AUTH_URL=http://localhost:3000

# AI — set your model (requires an Anthropic API key configured in your AI gateway)
AI_DEFAULT_MODEL=claude-sonnet-4-20250514

# Storage — "local" stores uploads on disk in development
BLOB_STORAGE_PROVIDER=local

# Encryption — must be exactly 32 bytes, change in production
ENCRYPTION_KEY=change-me-32-byte-key-here-pad00

# Ingestion worker
INGESTION_WORKER_URL=http://localhost:4000
INGESTION_WEBHOOK_SECRET=dev-secret-change-me

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
NODE_ENV=development
```

#### Optional services

These are not required for basic local development but enable additional features:

| Variable | Service | Purpose |
|----------|---------|---------|
| `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` | Google OAuth | "Sign in with Google" |
| `WHOOP_CLIENT_ID` / `WHOOP_CLIENT_SECRET` | Whoop API | Wearable data sync |
| `UPSTASH_REDIS_REST_URL` / `UPSTASH_REDIS_REST_TOKEN` | Upstash Redis | Rate limiting |
| `RESEND_API_KEY` | Resend | Transactional email |
| `NEXT_PUBLIC_GOOGLE_MAPS_API_KEY` | Google Maps | Testing site locator |

### 5. Run database migrations

```bash
pnpm db:migrate
```

Optionally, seed the database with sample data:

```bash
pnpm db:seed
```

### 6. Start the development servers

```bash
pnpm dev
```

This starts all services concurrently via Turborepo:

| Service | URL | Description |
|---------|-----|-------------|
| Web app | [http://localhost:3000](http://localhost:3000) | Next.js frontend & API |
| Ingestion worker | [http://localhost:4000](http://localhost:4000) | Background document processing |

### 7. Open the app

Visit [http://localhost:3000](http://localhost:3000) to create an account and start uploading health data.

## Useful Commands

```bash
pnpm dev              # Start all services in development mode
pnpm build            # Production build
pnpm lint             # Run linter across all packages
pnpm typecheck        # Type-check all packages
pnpm format           # Format code with Prettier

# Database
pnpm db:generate      # Generate a new migration after schema changes
pnpm db:migrate       # Apply pending migrations
pnpm db:seed          # Seed the database

# From packages/database
pnpm db:studio        # Open Drizzle Studio (visual database browser)

# From apps/web
pnpm test:e2e         # Run Playwright end-to-end tests
pnpm test:e2e:ui      # Run Playwright tests with interactive UI
```

## Deployment

Everything runs on a single **Oracle Cloud Always Free** VM (shared with the owner's other projects, 0 €/month):
PostgreSQL 16, the Next.js web app and the ingestion worker, each in Docker, behind Caddy with automatic HTTPS.
Only the web app is reachable from the Internet; the database and the worker stay on the project's internal Docker
network. The web app needs a Node.js server (auth, tRPC API), so it cannot be hosted on GitHub Pages.

The `Deploy backend` workflow (`.github/workflows/deploy-backend.yml`) updates the VM on every push to `main`.

1. Create the VM once by following [deploy/README.md](deploy/README.md) (Italian).
2. In **Settings → Secrets and variables → Actions** add:

   | Type | Name | Value |
   |---|---|---|
   | Variable | `VM_HOST` | public IP of the VM |
   | Secret | `VM_SSH_KEY` | private SSH key of the VM |
   | Secret | `POSTGRES_PASSWORD` | letters and digits only, e.g. `openssl rand -hex 24` |
   | Secret | `BETTER_AUTH_SECRET` | e.g. `openssl rand -hex 32` |
   | Secret | `ENCRYPTION_KEY` | **exactly 32 characters**, e.g. `openssl rand -hex 16` |
   | Secret | `INGESTION_WEBHOOK_SECRET` | shared secret between web app and worker, e.g. `openssl rand -hex 24` |
   | Secret | `AI_GATEWAY_API_KEY` | Vercel AI Gateway key used by the AI SDK |
   | Variable | `AI_DEFAULT_MODEL` | optional, defaults to `claude-sonnet-4-20250514` |
   | Variable | `API_HOST` | optional: your own domain (DNS `A` record to the VM) |

   Optional secrets, passed through when set: `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `WHOOP_CLIENT_ID`,
   `WHOOP_CLIENT_SECRET`, `WHOOP_WEBHOOK_SECRET`, `INTEGRATION_WEBHOOK_SECRET`, `RESEND_API_KEY`,
   `UPSTASH_REDIS_REST_URL`, `UPSTASH_REDIS_REST_TOKEN`, `BLOB_READ_WRITE_TOKEN` (with the variable
   `BLOB_STORAGE_PROVIDER=vercel`; by default uploads are stored in a Docker volume shared by web app and worker).

3. Run **Actions → Deploy backend → Run workflow**. Migrations are applied when the web container starts.
   The app answers on `https://openvitals.<IP with dashes>.sslip.io` (or `API_HOST`); use that URL as the
   redirect/webhook base for Google OAuth and Whoop.

Ingestion worker settings: `INGESTION_WORKER_URL` (web app → worker, `http://worker:4000` on the VM) and
`INGESTION_WEBHOOK_SECRET` (bearer token checked by the worker).

## Contributing

Contributions are welcome. Please open an issue to discuss your idea before submitting a pull request.

## License

OpenVitals is licensed under the [GNU General Public License v3.0](LICENSE).
