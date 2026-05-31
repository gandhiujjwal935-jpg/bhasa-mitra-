# BhaashaMitra — AI Language Teacher

An immersive AI-powered language learning app where a warm, animated Guru teaches users 13 Indian and world languages through structured lessons and live AI conversation sessions.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/language-teacher run dev` — run the frontend (port 23920)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string
- Required env: `AI_INTEGRATIONS_OPENAI_BASE_URL` + `AI_INTEGRATIONS_OPENAI_API_KEY` — Replit AI integrations (auto-provisioned)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS + Wouter routing + Framer Motion
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- AI: OpenAI GPT-5.4 via Replit AI Integrations (no user API key needed)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract (source of truth)
- `lib/db/src/schema/` — DB schema: conversations, messages, lessons, user_progress
- `artifacts/language-teacher/src/` — React frontend
- `artifacts/api-server/src/routes/` — Express route handlers
- `lib/integrations-openai-ai-server/` — OpenAI SDK client + utilities
- `lib/integrations-openai-ai-react/` — React hooks for voice/audio

## Architecture decisions

- Languages are defined as a static list in the API server (no DB table needed for language metadata)
- AI teacher conversations use GPT-5.4 with a structured JSON response format (reply, suggestions, correction, vocabulary)
- Lessons are stored in the DB seeded at setup, chat history is passed per-request (stateless for the /chat endpoint)
- OpenAI conversations (for the session feature) are persisted in DB with full message history
- DB uses `generatedAlwaysAsIdentity()` for auto-increment IDs (not serial)

## Product

**BhaashaMitra** supports 13 languages: Hindi, English, Sanskrit, Marwadi, Gujarati, Punjabi, Marathi, Telugu, Tamil, Kannada, Chinese, Japanese, Bihari.

Pages:
- `/` — Language discovery grid
- `/learn/:code` — Language dashboard (lessons, progress, daily challenge)
- `/lesson/:code/:id` — Lesson viewer with animated teacher and vocabulary flashcards
- `/session/:code` — Live AI conversation session with real-time teacher responses
- `/progress` — Overall learning progress dashboard

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- After OpenAPI spec changes, always run `pnpm --filter @workspace/api-spec run codegen`
- Component names in OpenAPI spec must NOT match `<OperationIdPascal>Body` (causes TS2308)
- `generatedAlwaysAsIdentity()` fields cannot be in `.omit()` calls — they're excluded from insert schema automatically
- Audio routes require Express body limit increase for large audio payloads

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
- See `.local/skills/ai-integrations-openai/SKILL.md` for OpenAI integration details
