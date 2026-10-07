---
name: reverse-app-prompt
description: Functional reverse-engineering of an existing application (SaaS, mobile, desktop, open-source) from everything public on the web — official site, docs, changelog, pricing, API, plus bug reports, feature requests, forums and store reviews — to produce a complete, ready-to-use functional prompt for Claude Code to build a new, equivalent or better application. Use this skill whenever the user wants to clone, rebuild, recreate, "make my own version of", build an alternative to, or reverse-engineer an app, write a spec / PRD from an existing product, or list every feature of a competitor to rebuild it — even if they never say "prompt". Also triggers in French: « cloner », « refaire », « créer une alternative à », « s'inspirer de », « cahier des charges à partir de <app> ».
---

# Reverse App Prompt

Goal: turn an existing application into an **exhaustive functional prompt** that Claude Code can execute to build a new application. The result's value rests on two things: **coverage** (no real feature forgotten) and **insight** (knowing what frustrates current users, so the new app does better).

## Language

Talk to the user and write every output (notes, final prompt) in **the user's language** — the language of their request — unless they ask for another one. This file is in English for maintainability only. Translate the template's section headings into the output language; keep the origin tags (`[Official]`, `[Inferred]`, `[Requested]`, `[Fix]`) in English so prompts stay comparable across languages.

## 1. Ask the questions

Ask the user, in a single message:

1. **Which application?** (name, and vendor if ambiguous)
2. **What are its official sites?** (website, docs, blog, GitHub… — "I don't know" is a valid answer: you'll find them)
3. **Public or internal use?** Public = a product open to anyone (like the original). Internal = a tool for one team or company. Default: public.
4. **Target tech stack?** The user can impose their own (e.g. "NestJS + Angular + PostgreSQL") or answer "suggest one": you'll then offer tailored options after the analysis (step 4b), because the right stack depends on what the research reveals (real-time, mobile, offline, API…).

Mention they can add other constraints (target platform, narrower scope, prompt language).

Don't re-ask anything the user already answered in their request.

### Internal mode

An internal tool doesn't need to convince or acquire users: everything that serves acquisition and monetization becomes dead weight that slows down the build. If the user picks internal use, the final prompt:

- **Authentication**: username + password only (hashed passwords, secure sessions). Accounts created by an administrator; the first admin is created at first launch (seed or environment variable). The admin can reset a password; users can change their own.
- **Removes**: public sign-up, onboarding (guided tours, checklists, demo data, welcome emails), email invitations, email verification, social login/SSO, email-based password recovery, plans/pricing/quotas/paywall/billing, free trials, marketing pages.
- **Also removes** 2FA (contradicts "username + password only") and self-service account deletion (replaced by admin deactivation/anonymization).
- **Keeps**: roles and permissions (still meaningful internally) and every business feature, including those reserved for the original's paid plans. Business emails (reminders, scheduled reports) become in-app notifications, with email optional when SMTP is configured.
- **Defaults**: one instance = one organization, self-hostable (Docker). The "Onboarding" journey in section 5 becomes "First launch" (admin creation, first workspace).
- User requests about a removed element (e.g. "simpler onboarding") go to "Out of scope" with the rest, not silently dropped.

Research doesn't change: keep reading the original's pricing and onboarding, since they reveal features. Filtering happens at writing time. List removed elements under "Out of scope" marked "internal use", so the choice is visible and reversible.

## 2. Research tools

Tool names below are Claude Code's; in another agent, use its equivalents.

- **Web search** (WebSearch) to discover sources, **page reading** (WebFetch) to read them. Fast and nothing to install; run several reads in parallel.
- **Controllable browser** (claude-in-chrome, Playwright MCP or equivalent) as a fallback, in three cases:
  - page reading returns an empty or truncated page because it's rendered in JavaScript (Canny/UserVoice/Featurebase boards, App Store/Google Play reviews, SPA docs);
  - page reading gets a **403 / anti-bot block** on a rich source (G2, TrustRadius, Capterra, SaaSHub, Reddit…): a real browser usually gets through. Don't settle for the search-result snippet when the full page is reachable this way;
  - a page read as plain text **looks inconsistent**: typically a pricing grid where every plan seems identical, because the check/cross marks are icons the extracted text doesn't show. Check it visually in the browser before deriving the free/paid split.

  In Claude Code, load the `claude-in-chrome` skill first. If no browser is available, mark the source as "not read" in the notes and move on. Don't use a logged-in session to access the user's private account content without their consent.
- If the application is open-source, its GitHub/GitLab repo is a goldmine (README, issues, discussions, `enhancement`/`bug` labels, roadmap, CHANGELOG). `github.com/<org>/<repo>/issues?q=...` pages read well with page reading.

## 3. Research — two tracks

**Output location**: if the user or the task imposes an output folder, use it. Otherwise, the session's working directory. Everything goes there: notes and final prompt.

Keep a notes file `<app-slug>-research/notes.md` in that folder, updated as you go (feature → full source URL, never just a site name: the prompt's appendix is built from these notes). Research is long: without these notes, context gets lost and features vanish at writing time. One line per finding, review sites and API sub-pages included:

```
- Recurring tasks reappear as soon as completed [Fix] — https://www.capterra.com/p/161732/KanbanFlow/reviews (2024 review)
- Webhook payloads are HMAC-signed [Official] — https://kanbanflow.com/api-docs/webhook-security
```

### Track A — What the application does (official sources)

By yield:

1. **Help center / documentation**: each article often describes one precise feature, with its rules and limits. Go through the whole table of contents before diving in.
2. **Pricing page**: the plan matrix reveals a near-complete feature list and the limits (quotas, seats, storage).
3. **Changelog / release notes / product blog**: recent or low-key features missing from marketing.
4. **API / webhook docs**: reveal the real **data model** (entities, fields, statuses, relations). Very valuable for the data model section.
5. **Feature, integration, keyboard shortcut, security/compliance and terms pages** (limits, roles, retention).
6. **Videos and tutorials** (titles and descriptions) for user journeys.

**No public help center?** (404, behind login, nonexistent) Don't stop there, it's common. Replace it, by yield: API docs (often the most precise source on rules and fields), FAQ embedded in pricing/feature pages, store listings (App Store, Google Play, Chrome Web Store: descriptions and release notes), third-party tutorials and YouTube videos, archived help center on web.archive.org. Note in the notes that official help was missing, so inferred elements are weighted accordingly.

### Track B — What users want and endure

Search with queries such as `"<app>" feature request`, `"<app>" bug`, `"<app>" roadmap`, `"<app>" vs`, `"<app>" alternative`, `site:reddit.com <app>`, `"<app>" canny`, `"<app>" uservoice`, plus the same in the app's main market language when it isn't English:

- **Feedback boards / public roadmaps** (Canny, UserVoice, Featurebase, Productboard, Upvoty, community forum): sort by votes, the most-voted requests are the best opportunities.
- **GitHub issues** (bugs and `enhancement`), discussions.
- **Reddit, Hacker News, Product Hunt**: concrete pain points, comparisons.
- **Reviews**: App Store, Google Play, G2, Capterra, Trustpilot — focus on 1-3 star reviews, they list what's missing.
- **AlternativeTo / "X vs Y" comparisons**: features competitors have that the app lacks.

**Cover at least three source families** among: feedback board/official forum, GitHub, Reddit/HN, store reviews, review sites (G2, Capterra…), comparisons. Review sites alone give a biased view (short, often old reviews); Reddit and feedback boards reveal real requests and their popularity. For Reddit, also search the domain's subreddits (r/productivity, r/projectmanagement…), not just the app's name. If a family yields nothing after a serious search, note it rather than skipping it silently.

For each recurring bug, record the **expected behavior** (that's what goes into the prompt), not just the symptom.

### When to stop

Go wide (often 30 to 80 pages depending on the app's size). Stop when new pages stop bringing new features. For a large application, you can run two sub-agents in parallel (track A and track B), each writing its own notes file, if your environment supports sub-agents.

## 4. Synthesize

From the notes, build the inventory, tagging each element's origin — this is what lets the reader tell faithful from improved:

- `[Official]` documented by the vendor
- `[Inferred]` deduced (e.g. from the API or a screenshot), to confirm
- `[Requested]` user feature request (give popularity when known)
- `[Fix]` behavior corrected relative to a known bug of the original

**Unreliable sources**: discard pages that look auto-generated (generic directory listings or "reviews" with no concrete detail, often copied from site to site) whenever they contradict an official source or claim a feature nothing else confirms. A feature appearing only in such a source stays out of the prompt, or goes in as `[Inferred]` with its source named. Log discarded sources in the notes.

**Conflicting sources** (e.g. a feature free per an old review, paid per the pricing page): the most recent official source wins, since reviews often describe an outdated offer. Don't decide silently: list each conflict in an "Open questions" subsection of section 7 (or the relevant section), with both sources and the decision taken.

Then group into coherent functional modules, deduce roles, entities and their relations, and prioritize: **MVP** (the core needed for the app to be usable), **V1**, **V2+**. Highly voted user requests can move up to V1 if they're a real differentiator.

## 4b. Choose the stack

If the user imposed a stack, keep it as is: don't "correct" it, just flag a real friction point in one line (e.g. an offline need with a 100% server-side stack).

Otherwise, read `references/stacks.md`, infer the app's profile from the synthesis (collaborative web, real-time, mobile, offline, desktop, internal CRUD tool…) and offer **2 or 3 tailored options**, recommended one first, each with one sentence on why it fits this particular app. Use the multiple-choice question tool (AskUserQuestion in Claude Code) if available; the user must always be able to type their own stack. If nobody can answer (autonomous run), take the recommended option and say so in the prompt.

## 5. Write the prompt

Read `references/prompt-template.md` and follow its structure. What matters:

- **Functional, not technical**: describe the *what* and the business rules, with verifiable acceptance criteria. The chosen stack goes in section 13; the rest of the prompt doesn't depend on it.
- **Self-contained**: Claude Code will only have this prompt. No "like in <app>" without explaining the behavior.
- **No reuse of the original's identity**: new name (suggest a code name), no logo, no copied marketing copy, no reproduced visual identity. We rebuild features, not a brand. References to the original stay in the context section and sources.
- **Exhaustive yet readable**: lists and tables over prose. A long prompt is normal here; a vague one isn't.

Save the result to `<app-slug>-prompt.md` (output folder defined above), with sources in the appendix as **full URLs** (no shortened paths: the reader must be able to click). In the chat, give a short summary: number of modules / features, top 5 improvements from track B, file path, and the uncertain areas (`[Inferred]`) to validate.
