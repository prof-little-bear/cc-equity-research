[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/prof-little-bear-cc-equity-research-badge.png)](https://mseep.ai/app/prof-little-bear-cc-equity-research)

# CC Equity Research Toolkits

**English** | [日本語](README.ja.md) | [简体中文](README.zh-CN.md)

**A self-contained repo that turns your Claude Code into an equity-research agent. Unlocks Anthropic's official equity-research skills for every investor — a single data MCP that replaces a $150K-per-year institutional data stack, and a personalization layer that caters to sell-side analysts, portfolio managers, retail investors, and academic economists alike.**

<br>

![/discover running the humanoid-robotics supply-chain skill — parallel agents map upstream picks-and-shovels across US and JP tickers](assets/screenshots/cc-equity.png)

Three components make it work:

1. **Skill library.** 24 analysis workflows — Anthropic's official Apache-licensed equity-research bundle (9 skills) plus a community-maintained library (15 skills). Covers idea discovery, single-company deep dives, position monitoring, and macro research.

2. **Accessible interface.** Claude speaks to you according to your financial proficiency and preferences — adapting tone, depth, and jargon density to match a sell-side analyst, a portfolio manager, a retail investor, or an academic economist. Four unified slash commands — `/discover`, `/analyze`, `/monitor`, `/macro` — auto-route plain-language requests across all 24 skills.

3. **Single data MCP.** `drillr` consolidates everything the skills need: structured financial metrics, SEC filings, earnings-call transcripts, company ontology (suppliers / customers / competitors), alternative data (federal contracts, hiring, patents, trade flows), and macro / market context. Covers US and Japanese equities. Generous free quota for individual users.

---

## Why we build this:

Anthropic recently open-sourced an excellent [equity-research skill bundle](https://github.com/anthropics/financial-services/tree/main/plugins/vertical-plugins/equity-research) — nine institutional workflow templates (initiation note, earnings analysis, catalyst calendar, morning note, thesis tracker, and more). They significantly raise Claude Code's ceiling for equity work. Two gaps remain.

1. **Expensive data connector.** The Anthropic skills are pure methodology — no data ships with them, so you have to wire in your own connectors. Anthropic's official [reference repo](https://github.com/anthropics/financial-services/blob/main/plugins/vertical-plugins/financial-analysis/.mcp.json) wires the skills to **eleven institutional MCPs** — FactSet, LSEG, S&P Global, Morningstar, Moody's, PitchBook, and others — typically **$15–30K per seat per year each**, well over $150K combined. Out of reach for independent analysts, academic economists, and anyone running serious workflows without an institutional budget.

2. **Targets sell-side professionals, no accessibility layer.** The bundle's templates are built around the daily output of an equity research desk — initiation notes, morning notes, earnings previews. The concepts are universal; the *jargons* (A/E year notation, basis-point shorthand, sell-side report shapes) read arcane to retail investors and to academics doing exploratory company-level work. Out of the box, the agent doesn't adapt to the user's financial fluency or preferred tone.

This project addresses both. The `drillr` MCP consolidates the eleven-MCP stack into a single connector with a generous free quota; a memory-driven interface layer adapts the output register to whoever is at the keyboard.

---

## Install

You need [Claude Code](https://claude.com/claude-code) installed.

```bash
git clone https://github.com/prof-little-bear/cc-equity-research.git
cd cc-equity-research
claude
```

Inside Claude Code, run `/mcp` to check the `drillr` connection and authenticate if prompted (the repo's `.mcp.json` declares the server — Claude Code picks it up on launch). After that you're ready — type what you want, or fire one of the four slash commands.

---

## Interface — four slash commands

Each command opens a short menu of lenses in that category. Pick one by name, or just describe what you want and the dispatcher routes from there.

| Command | Category | What it covers |
|---|---|---|
| `/discover` | Idea generation | themes, supply chain, alt-plays, federal contracts; plus Anthropic's `idea-generation` (systematic screens) and `sector-overview` |
| `/analyze` | Single-company deep work | business model, earnings scorecard, forensics, reporting drift, management; plus Anthropic's `initiating-coverage`, `earnings-preview`, `earnings-analysis`, `model-update` |
| `/monitor` | Position tracking | watchlist, thesis check, event radar; plus Anthropic's `thesis-tracker`, `catalyst-calendar`, `morning-note` |
| `/macro` | Economic research | yield curve, trade flows, labor market |

**Plain language works too.** *"Run forensics on NKE"*, *"what's coming up for PLTR over the next 6 weeks"*, *"is the labor market softening"* — Claude routes through `CLAUDE.md`'s intent map directly to the skill. The slash commands are the discoverability layer; plain language is the power-user layer. Same skills underneath.

Examples: `/analyze NKE forensics` → runs financial forensics on Nike. `/macro` → opens the macro menu. `/discover what's working in AI infra` → routes to `themes`.

**The slash surface stays at four commands no matter how many skills get added** — new skills are added inside the dispatchers, not as new commands.

---

## Modes — adapting to the user

Two project-local files shape every response. This is the layer that addresses Problem #2 — same analytical rigor, different register depending on who's at the keyboard.

- **`.claude/mode.md`** — `new` (default) shows orientation on session start; `experienced` skips it. Switch by editing the file or telling Claude "I'm experienced now."
- **`.claude/style.md`** — four fields control how Claude communicates: `experience` (experienced / intermediate / learning), `depth` (quick / balanced / deep), `tone` (professional / institutional / conversational / educational), and optional `coverage` (sector focus). Defaults are sophisticated-but-accessible.

Claude reads both at session start, applies them every turn, and absorbs preference shifts mid-conversation. Start using A/E notation casually and `tone` promotes to `institutional`. Ask what a term means and `experience` shifts to `intermediate`. The file updates and the change is confirmed in one line.

> Note: these files are *project-local* — they live inside the repo, not in Claude Code's cross-session auto-memory. Cloning to a different machine means starting fresh unless you sync the repo.

---

## Skills

**Anthropic bundle** (`anthropic-equity-research-skills/`) — nine workflow templates vendored from [`anthropics/financial-services`](https://github.com/anthropics/financial-services) (Apache 2.0): `initiating-coverage`, `earnings-preview`, `earnings-analysis`, `model-update`, `morning-note`, `catalyst-calendar`, `thesis-tracker`, `idea-generation`, `sector-overview`.

**Community extension** (`community-skills/`) — fifteen analyst-contributed lenses across four areas: `discover/` (themes, supply-chain, alt-plays, gov-contracts), `analyze/` (business-model, earnings-scorecard, financial-forensics, reporting-quality, management), `monitor/` (watchlist, thesis-check, event-radar), `economic-research/` (yield-curve, trade-flows, labor-market).

Each skill is a short markdown file — read one to see exactly what it does.

---

## Data — the [`drillr`](https://drillr.ai) MCP

One MCP backs every skill. Six data domains:

- **Structured fundamentals** — statements, 60+ standardized metrics, consensus estimates
- **SEC filings** — 10-K, 10-Q, 8-K, proxy, S-1, S-4 with full-text search
- **Earnings calls** — transcripts and structured summaries
- **Company ontology** — suppliers, customers, competitors, peer groups, founder backgrounds
- **Alternative data** — federal contracts, hiring, web/app metrics, patents, trade flows, insider transactions
- **Macro and market context** — rates, credit, labor, sentiment, indices, commodities, FX, crypto

Sourced directly from primary sources (SEC EDGAR, company IR pages, government databases, customs filings, public market venues) via agentic AI — not resold proprietary feeds. **Generous free quota for individual users**; no FactSet, LSEG, S&P Global, or Morningstar subscription required.

Coverage: US equities, Japan equities, and ADRs. You don't write SQL — describe what you need, and Claude pulls the data when a skill runs.

---

## Contributing

Community-skill contributions are very welcome — they're the part of the toolkit that gets sharper as working analysts share what they actually do.

Three small edits for any new community skill:
1. Add the skill file itself in `community-skills/<area>/`
2. Add a one-line entry to the capability map in `CLAUDE.md`
3. Add a row to the matching dispatcher's menu in `.claude/commands/<area>.md`

Then submit a PR. The Anthropic bundle is vendored upstream — suggested changes there belong in [`anthropics/financial-services`](https://github.com/anthropics/financial-services), not in this repo.

See `CONTRIBUTING.md` for the skill template, what good skills look like, and review expectations.

---

## License

The toolkit (community skills, scaffolding, dispatchers, documentation) is licensed under Apache 2.0 — see the top-level `LICENSE` file. The vendored Anthropic equity-research bundle is also Apache 2.0; see `anthropic-equity-research-skills/NOTICE.md` for attribution and the upstream-sync command.

---

## About

Used in this project:

- **[anthropics/financial-services](https://github.com/anthropics/financial-services)** — Anthropic's open-source equity-research skill bundle, vendored at `anthropic-equity-research-skills/` (Apache 2.0)
- **[Drillr](https://drillr.ai)** — the single data MCP backing every skill (fundamentals, SEC filings, company ontology, alt-data, macro / market signals)
