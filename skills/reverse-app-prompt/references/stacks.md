# Tech stack catalog by application profile

Starting point for offering 2-3 options. Choose based on the needs **revealed by the research**, not on fashion: a mostly-CRUD app doesn't need a distributed architecture. Adapt versions and components to the state of the art at the time of the suggestion.

| Profile | Option | Composition | Why |
|---|---|---|---|
| Collaborative web / SaaS (real-time, multi-user) | **Full-stack TypeScript** | Next.js + TypeScript, PostgreSQL + Drizzle/Prisma, Better Auth/Auth.js, WebSockets (or Supabase Realtime), Tailwind + shadcn/ui, Playwright | One language, huge ecosystem, real-time under control |
| | **BaaS** | React (Vite) + Supabase (Postgres, auth, realtime, storage) | Fastest MVP, little back end to write; vendor dependency |
| Large business domain, public API, back-end team | **API-first** | NestJS + PostgreSQL + TypeORM/Prisma, separate React or Angular front end, generated OpenAPI | Modules and dependency injection give structure, API documented out of the box |
| Internal tool / CRUD / back office | **Productive monolith** | Django + HTMX (or Laravel + Livewire), PostgreSQL or SQLite, Docker Compose | Admin, auth, ORM, forms included; very little JavaScript |
| | **Lightweight full-stack TS** | Next.js + SQLite (Drizzle) + username/password auth, Docker | A single image to deploy, zero external services |
| Mobile (iOS + Android) | **Cross-platform JS** | Expo (React Native) + TypeScript, Supabase or NestJS back end | Code shared with the web, OTA updates |
| | **Cross-platform native** | Flutter + Firebase/Supabase | Very consistent UI, near-native performance |
| Offline / local-first | **Local-first PWA** | React + IndexedDB (Dexie) + sync engine (PowerSync, ElectricSQL, Replicache) + Postgres | Works without network, reliable sync |
| Desktop | **Tauri** | Tauri + React/Svelte, SQLite | Lightweight binaries, Rust security |
| | **Electron** | Electron + React | Full Node access, mature ecosystem; heavier |
| Content / public site + app | **Hybrid** | Astro or Next.js for public pages, app behind auth | SEO and performance on public pages |

Cross-cutting notes:
- **Internal use**: favor simple deployment (one container, one database, no SaaS dependency) and built-in username/password auth.
- **Pomodoro, timers, notifications**: plan a robust client-side mechanism (Service Worker / native notifications) whatever the stack.
- **API + webhooks** in scope: favor a stack that generates the OpenAPI spec.
- If the user already uses a stack in their other projects (visible in the current repo), offer it first: consistency is worth more than a theoretical gain.
