# VC Market Mapping Skills

[Skills](https://docs.anthropic.com/en/docs/claude-code/skills) are reusable prompt templates for Claude Code, Anthropic's agentic coding tool. This pack helps VCs and investors build competitive market maps: define markets, discover competitors across 4 levels, and enrich with positioning axes and competitive intelligence.

Maintained by [Extruct AI](https://www.extruct.ai).

> **These skills are templates.** They show one way to run VC market mapping workflows using Extruct and Claude Code. Fork them, adapt to your workflow. The skill patterns are the value, the specific integrations are up to you.

## Install

```
npx skills add extruct-ai/vc-skills
```

**Where skills live:** Once installed, skills are stored in `~/.claude/skills/`. They become globally available across all your projects. Install once, use everywhere.

## Quick Start

After installing, just describe what you need:

**Start from a company:**

```
I want to map the competitive landscape around getenter.ai.
They do AI legal SaaS in Brazil.
```

**Start from a market category:**

```
Map the AI code review market.
I want to understand who the players are
and how they position themselves.
```

Each prompt triggers the skills automatically. Claude will research, propose a market definition, discover competitors, and enrich them with positioning data.

## Skills

Two skills that work together as a pipeline. Each also works independently.

```
market-definition → competitive-landscape
```

| Skill | Description |
|-------|-------------|
| **vc-market-definition** | Define market context: positioning axes, capability areas, buyer personas. Two entry modes: start from a company domain or a market category. |
| **vc-competitive-landscape** | Full competitor analysis: scope 4 levels (direct, indirect, adjacent, substitute), discover via semantic + lookalike + deep search, enrich with positioning axes and competitive intelligence. |

### Competitor Levels

| Level | What it captures | Search strategy |
|-------|-----------------|----------------|
| **Direct** | Same product, same problem, same buyer | Describe the PRODUCT |
| **Indirect** | Same problem, different approach | Describe the PROBLEM |
| **Adjacent** | Could expand into this market | Similar tech / same buyer / platform risk |
| **Substitute** | Non-software alternatives | Narrative + software-adjacent search |

### Enrichment Columns

Each competitor table gets enriched with 11 columns:

**Positioning Axes (5):** Scored 1-5 on market-specific axes (e.g., SMB vs Enterprise, AI-Native vs Traditional, Point Solution vs Platform).

**Competitive Intelligence (6):** Review Sentiment, Analyst Positioning, Recent Funding, Hiring Signals, Recent News, Target Market.

## About Extruct

[Extruct](https://www.extruct.ai): API-first company search and lookalikes engine.

**Core capabilities:**
- **Instant Search**: semantic company search (free, unlimited)
- **Lookalike Search**: find companies similar to your best accounts (free, unlimited)
- **Deep Search**: AI-powered discovery with criteria scoring (1 credit/match)
- **AI Tables**: research any data point per company with AI agents (1 credit/cell)

**Pricing:** from $59/mo for 1K credits. Free trial with 100 credits. See [extruct.ai/pricing](https://www.extruct.ai/pricing).

---

## Reference

### Output Artifacts

Skills automatically create a `claude-code-gtm/vc-maps/` directory with all intermediate files:

```
claude-code-gtm/vc-maps/{map-slug}/
├── market_context.md          ← axes, capabilities, buyer personas
└── competitor_scoping.md      ← table IDs, search methodology, company counts
```

Enriched company data lives in Extruct tables (one per competitor level).

### Environment Variables

Only the Extruct API token is needed.

| Variable | Service | Used by |
|----------|---------|---------|
| `EXTRUCT_API_TOKEN` | [Extruct API](https://www.extruct.ai/docs) | vc-market-definition, vc-competitive-landscape |
