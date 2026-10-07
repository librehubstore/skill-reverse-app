# Final prompt template

Keep this section order. Translate headings and labels into the output language (the user's language); keep the origin tags in English. Drop a section only if it truly doesn't apply (e.g. no API for a 100% local app) and say so in one line.

```markdown
# <Code name> — Functional specification for Claude Code

## 0. Instructions for Claude Code
- Read this entire document before writing any code.
- Start by proposing a phased implementation plan (MVP → V1 → V2) using the stack in section 13, then wait for approval.
- Build the MVP first, end to end, with tests covering the acceptance criteria.
- When something is ambiguous, apply the simplest assumption and record it in a DECISIONS.md file.

## 1. Context and goal
- Reference application: <app> (<vendor>) — <one sentence on what it does>.
- Goal: build an original application offering these features while fixing its known flaws.
- Use: <public | internal — tool for one team, no sign-up or onboarding, username/password authentication, accounts created by an admin>.
- New code name: <name>. Reuse none of the original's brand, logo, copy or visual identity.
- Differentiating value proposition: <2-3 bullets drawn from user requests / pain points>.

## 2. Users, roles and permissions
Table: role | description | can do | cannot do.

## 3. Scope and prioritization
Summary table: module | feature | priority (MVP/V1/V2) | origin ([Official]/[Inferred]/[Requested]/[Fix]).

## 4. Functional modules
For each module:
### 4.x <Module>
- **Purpose**: …
- **User stories**: "As a <role>, I want … so that …"
- **Business rules**: validations, limits, states and transitions, edge cases.
- **Acceptance criteria**: verifiable list (Given/When/Then or testable bullets).
- **Improvements over the original**: related [Requested]/[Fix] items.

## 5. Key user journeys
Onboarding (internal use: "First launch"), main flow, collaboration/sharing flow, etc. — numbered steps.

## 6. Data model
Entities, main fields (type, required), relations, statuses/enums. Mermaid `erDiagram` when useful.

## 7. Plans, quotas and limits
Internal use: replace this section with one line "Not applicable (internal use)" and keep only the "Open questions" subsection.
Public use, if the original is freemium/paid: what is limited and how (reproduce or simplify — say which).

### Open questions
Source conflicts and important [Inferred] items: item | source A | source B | decision taken.

## 8. Integrations, import/export, API
Third-party integrations, import/export formats, planned public API and webhooks.

## 9. Notifications
Triggering events, channels (in-app, email, push), preferences.

## 10. Non-functional requirements
Performance, security (auth, GDPR), accessibility (WCAG AA), i18n, responsive/mobile, offline, backups.

## 11. Pain points of the original to avoid
Recurring bugs and frustrations found, with the expected behavior in the new app.

## 12. Out of scope
What is deliberately not built (and why).

## 13. Tech stack
Chosen stack (imposed by the user, picked from the suggestions, or recommended by default — say which): front end, back end, database, auth, real-time/sync if needed, hosting/deployment, tests. One line of justification per structural choice.

## Appendix — Sources
Full, clickable URLs, grouped: official / feedback & bugs / reviews & comparisons. Mention unread sources (403, login) and source families that yielded nothing.
```
