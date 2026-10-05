# reverse-app-prompt

An AI agent skill (standard `SKILL.md` format) that **reverse-engineers the features of an existing application** from everything publicly available on the web, then writes a **complete functional prompt**, ready to hand to Claude Code (or any coding agent) to build a new, equivalent or better application.

> The skill talks to the user and writes the prompt in **French** by default. Ask for another language and it will follow.

## What it does

1. **Asks 4 questions**: the application, its official sites, the intended use (public or internal), and the target tech stack (yours, or tailored suggestions after the analysis).
2. **Researches in two tracks**:
   - **Official**: docs, help center, pricing, changelog, API, integrations.
   - **Users**: feedback boards (Canny, UserVoice…), GitHub issues, Reddit, Hacker News, app store reviews, G2, Capterra, comparisons.
3. **Synthesizes**: each feature is tagged by origin:
   - `[Officiel]`: documented by the vendor;
   - `[Déduit]`: inferred, to be confirmed;
   - `[Demande]`: requested by users;
   - `[Correctif]`: known bug in the original, to be fixed.

   Features are then prioritized as MVP, V1 or V2, and conflicts between sources are listed under "Points à valider".
4. **Writes the final prompt** `<app>-prompt.md`:
   - roles and permissions;
   - modules with user stories, business rules and acceptance criteria;
   - data model;
   - API;
   - pain points of the original to avoid;
   - tech stack;
   - sources.

**Internal mode**: no sign-up, onboarding or paid plans. Username/password login, with accounts created by an administrator.

The new application gets a code name and reuses none of the original's brand, logo or copy: it rebuilds features, not an identity.

## Requirements

The agent must be able to **search the web** and **read web pages**. A **controllable browser** is recommended for pages rendered in JavaScript or protected against bots (Claude in Chrome, Playwright MCP, or your agent's equivalent). Without web access the skill cannot work.

## Installation

### Automatic (multi-agent)

Both installers find the skill under `skills/` and install it for the agent you pick:

```bash
# "skills" CLI (Claude Code, Codex, Cursor, Copilot, Gemini CLI, OpenCode, Cline…)
npx skills add librehubstore/skill-reverse-app

# GitHub CLI (recent version with the skill command)
gh skill install librehubstore/skill-reverse-app reverse-app-prompt --agent claude-code --scope user
# --agent: claude-code, github-copilot, cursor, codex, gemini-cli, opencode…
```

### Claude Code: plugin marketplace

In Claude Code:

```
/plugin marketplace add librehubstore/skill-reverse-app
/plugin install reverse-app-prompt@reverse-app-prompt
```

Get updates with `/plugin marketplace update`.

### Manual (any agent)

Clone the repository, then copy `skills/reverse-app-prompt` into your agent's skills directory:

| Agent | User (all projects) | Project only |
|---|---|---|
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| OpenAI Codex | `~/.agents/skills/` | `.agents/skills/` |
| GitHub Copilot (VS Code, CLI) | `~/.copilot/skills/` | `.github/skills/` |
| Cursor | `~/.cursor/skills/` | `.cursor/skills/` |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` |
| Other `SKILL.md`-compatible agent | `~/.agents/skills/` (generic location most agents read) | `.agents/skills/` |

Example for Claude Code:

```bash
# macOS / Linux / Git Bash
git clone https://github.com/librehubstore/skill-reverse-app.git
mkdir -p ~/.claude/skills && cp -r skill-reverse-app/skills/reverse-app-prompt ~/.claude/skills/
```

```powershell
# Windows PowerShell
git clone https://github.com/librehubstore/skill-reverse-app.git
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse skill-reverse-app\skills\reverse-app-prompt "$HOME\.claude\skills\"
```

For another agent, replace `~/.claude/skills` with the path from the table. Restart the agent so it picks up the skill.

### Claude.ai and Claude Desktop

1. Download the repository and zip the `skills/reverse-app-prompt` folder as `reverse-app-prompt.zip`, with the folder itself at the root of the ZIP.
2. Open **Settings → Capabilities → Skills**, click **Upload skill** and pick the ZIP.
3. Enable web search, and Claude in Chrome if you have it.

## Usage

Just ask; the skill triggers on its own:

> I want to build an internal alternative to KanbanFlow, suggest a stack.

> Write the full prompt to rebuild Toggl Track with NestJS + Angular.

The prompt is written to `<app>-prompt.md` and the research notes to `<app>-research/notes.md`, in the current folder. A full analysis usually takes 10 to 20 minutes.

## Repository layout

```
skills/reverse-app-prompt/
├── SKILL.md                     # full workflow
└── references/
    ├── prompt-template.md       # final prompt template
    └── stacks.md                # tech stack catalog by application profile
.claude-plugin/marketplace.json  # Claude Code marketplace
evals/evals.json                 # skill test cases
```

## Responsible use

The skill only reads public information and reproduces **features**, not code, copy, visuals or trademarks. Check the terms of use of the sites it visits and the applicable law (intellectual property, trademarks) before commercializing a derived application.

## License

[MIT](LICENSE)
