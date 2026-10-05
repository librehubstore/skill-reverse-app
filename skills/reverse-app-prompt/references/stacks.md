# Catalogue de stacks par profil d'application

Point de départ pour proposer 2-3 options. Choisis selon les besoins **révélés par la recherche**, pas selon la mode : une app majoritairement CRUD n'a pas besoin d'une architecture distribuée. Adapte les versions et composants à l'état de l'art au moment de la proposition.

| Profil | Option | Composition | Pourquoi |
|---|---|---|---|
| Web collaboratif / SaaS (temps réel, multi-utilisateurs) | **Full-stack TypeScript** | Next.js + TypeScript, PostgreSQL + Drizzle/Prisma, Better Auth/Auth.js, WebSockets (ou Supabase Realtime), Tailwind + shadcn/ui, Playwright | Un seul langage, écosystème énorme, temps réel maîtrisé |
| | **BaaS** | React (Vite) + Supabase (Postgres, auth, realtime, stockage) | MVP le plus rapide, peu de back-end à écrire ; dépendance au fournisseur |
| Gros domaine métier, API publique, équipe back-end | **API-first** | NestJS + PostgreSQL + TypeORM/Prisma, front React ou Angular séparé, OpenAPI généré | Modules et injection de dépendances structurants, API documentée d'office |
| Outil interne / CRUD / back-office | **Monolithe productif** | Django + HTMX (ou Laravel + Livewire), PostgreSQL ou SQLite, Docker Compose | Admin, auth, ORM, formulaires inclus ; très peu de JavaScript |
| | **Full-stack TS léger** | Next.js + SQLite (Drizzle) + auth identifiant/mot de passe, Docker | Une seule image à déployer, zéro service externe |
| Mobile (iOS + Android) | **Cross-platform JS** | Expo (React Native) + TypeScript, back-end Supabase ou NestJS | Partage de code avec le web, mises à jour OTA |
| | **Cross-platform natif** | Flutter + Firebase/Supabase | UI très homogène, performances proches du natif |
| Hors-ligne / local-first | **PWA local-first** | React + IndexedDB (Dexie) + moteur de sync (PowerSync, ElectricSQL, Replicache) + Postgres | Fonctionne sans réseau, synchronisation fiable |
| Desktop | **Tauri** | Tauri + React/Svelte, SQLite | Binaires légers, sécurité Rust |
| | **Electron** | Electron + React | Accès complet à Node, écosystème mûr ; plus lourd |
| Contenu / site public + app | **Hybride** | Astro ou Next.js pour le public, app derrière auth | SEO et performances sur les pages publiques |

Repères transverses :
- **Usage interne** : privilégie la simplicité de déploiement (un conteneur, une base, pas de dépendance SaaS) et une auth identifiant/mot de passe intégrée.
- **Pomodoro, minuteurs, notifications** : prévoir un mécanisme côté client robuste (Service Worker / notifications natives) quelle que soit la stack.
- **API + webhooks** dans le périmètre : favorise une stack qui génère la spec OpenAPI.
- Si l'utilisateur a déjà une stack dans ses autres projets (visible dans le dépôt courant), propose-la en premier : la cohérence vaut plus qu'un gain théorique.
