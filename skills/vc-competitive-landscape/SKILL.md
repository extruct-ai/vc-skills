---
name: competitive-landscape
description: >
  Full competitor analysis for VC market maps: scope competitors at 4 levels
  (direct, indirect, adjacent, substitute), discover companies via Extruct API
  with a two-pass strategy (fast search first, deep search where needed), enrich
  with positioning axes and competitive intelligence, and build feature comparison
  matrices. Triggers on: "find competitors", "scope competitors", "competitive
  landscape", "competitor analysis", "discover competitors", "competitive
  enrichment", "competitor search", "build landscape", "competitor discovery".
---

# Competitive Landscape

Scope, discover, enrich, and compare competitors across 4 levels. This is the core skill — it takes a market definition and produces enriched Extruct tables and feature comparison matrices.

## Environment

| Variable | Service |
|----------|---------|
| `EXTRUCT_API_TOKEN` | Extruct API |

Base URL: `https://api.extruct.ai/v1`

## Official API Reference

- https://www.extruct.ai/docs/api-reference/introduction

## Inputs

| Input | Source | Required |
|-------|--------|----------|
| Market context | `claude-code-gtm/vc-maps/{map-slug}/market_context.md` | yes |
| User knowledge of competitors | User input | recommended |

## Workflow

This skill has 4 phases. Each phase pauses for user review before proceeding.

---

### Phase A: Competitor Scoping + Search Query Design

Read `market_context.md`. Extract: market description, positioning axes, capability areas, seed companies, geographic scope, stage focus.

For each of the 4 competitor levels, draft the scoping and search queries.

#### Level 1: Direct Competitors

**Definition:** Products that solve the same problem for the same users in the same way.

**Query design — describe the PRODUCT, not the problem:**
- Template: "Companies that offer [specific product type] for [target user] to [specific function]"
- Be specific about the product category and core capability
- Write 2-3 queries from different angles on the same product

**Example queries (AI code review market):**
1. "Companies that offer AI-powered code review tools for engineering teams"
2. "Automated pull request review and code analysis platforms"
3. "Developer tools for AI-assisted code quality and review automation"

**Lookalike seeds:** Use seed companies from market context + any the user names.

**Discovery criteria (for Pass 2 if needed):** Design 3-5 criteria from the capability areas in market context.

#### Level 2: Indirect Competitors

**Definition:** Products that solve the same problem but with a fundamentally different approach.

**Query design — describe the PROBLEM, not the product:**
- Template: "Companies that help [target user] solve [problem] using [different approach]"
- Write one query per distinct alternative approach
- Focus on the workflow and pain point

**Example queries (AI code review market):**
1. "Companies helping engineering teams maintain code quality through static analysis and linting tools"
2. "Developer education and training platforms focused on code quality best practices"
3. "Peer code review workflow tools and collaboration platforms for developers"

**Discovery criteria (for Pass 2 if needed):** Focused on problem overlap, NOT product similarity.

#### Level 3: Adjacent Competitors

**Definition:** Products that don't compete today but could expand into this market.

**Three query sub-strategies:**

A. Similar technology, different use case:
- "Companies using [same core tech] for [adjacent use case]"

B. Same customers, different product:
- "Companies selling [different product] to [same buyer persona]"

C. Platform expansion risk:
- "Platforms for [broader category] that include or could add [this capability]"

Write 1 query per sub-strategy (2-3 total).

**Lookalike:** Run from direct competitors — finds similar-profile companies in adjacent spaces.

#### Level 4: Substitute Solutions

**Definition:** Entirely different ways users solve the underlying need.

**Two parts:**

1. **Narrative section** — ask user: "What do companies do instead of using a product like this? Think: hiring people, using general-purpose tools (Excel, email), outsourcing, or ignoring the problem."

2. **Software-adjacent search** — 1-2 queries for general-purpose tools that subsume the need:
   - "General-purpose [tool type] that includes [relevant capability] as a feature"

#### Present scoping to user

Show the complete scoping plan:

```
Level: Direct Competitors
Definition: [...]
Known companies: [from user + seed list]
Semantic queries:
  1. "[query]"
  2. "[query]"
  3. "[query]"
Lookalike seeds: [domains]
Discovery criteria (for Pass 2): [criteria]

Level: Indirect Competitors
...
```

Ask once: "Does this scoping look right? Any competitors or approaches I'm missing?"

If user confirms, proceed immediately to Phase B. Do not ask multiple follow-up questions.

Save to `claude-code-gtm/vc-maps/{map-slug}/competitor_scoping.md`.

---

### Phase B: Pass 1 — Fast Discovery (Semantic + Lookalike)

Execute the search plan from Phase A using fast methods only.

**Step 1: Run semantic searches**

For each level, run each query via `GET /companies/search`:
- Use the queries designed in Phase A
- Apply filters from market context (geography, size, founding year)
- `limit=100` per query (up to `200` for direct competitors if needed)

**Step 2: Run lookalike searches**

For levels with seed companies, run `GET /companies/{seed_domain}/similar`:
- Apply matching filters
- `limit=100`

**Step 3: Merge and deduplicate**

Per level:
- Combine semantic + lookalike results
- Deduplicate by domain within the level

Across levels:
- If a company appears in multiple levels, assign to the highest-priority level
- Priority: direct > indirect > adjacent > substitute

**Step 4: Create tables, upload, and assess coverage**

Immediately create tables and upload — do NOT ask permission first:
- `POST /tables` with `kind: "company"`, `name: "{map-slug} — {level} competitors"`
- Upload domains in batches of 50 via `POST /tables/{id}/rows` with 0.5s between batches

Then present coverage assessment:

| Level | Semantic | Lookalike | After Dedup | Coverage |
|-------|----------|-----------|-------------|----------|
| Direct | N | N | N | Sufficient/Thin/Gaps |
| Indirect | N | N | N | ... |
| Adjacent | N | N | N | ... |
| Substitute | N | -- | N | ... |

**Coverage signals:**
- **Sufficient:** 20+ companies, recognizable names present, query overlap confirms category
- **Thin:** <10 companies, few recognizable names, low query overlap
- **Gaps:** Known competitors missing, sub-segments not represented

**Auto-proceed logic:**
- If all levels are "Sufficient" → skip deep search, go straight to Phase D (enrichment)
- If any level is "Thin" or "Gaps" → recommend Deep Discovery for that level and ask: "Direct competitors coverage is thin. Want me to run Deep Discovery to find more? Or proceed with what we have?"
- Do NOT ask "should I create tables?" — just create them
- Do NOT offer "filter down" as an option — the user can always remove companies from the table later

---

### Phase C: Pass 2 — Deep Discovery (Optional)

Only run for levels where the user requests deeper coverage.

**Step 1: Design Discovery criteria**

Based on what was found (and missing) in Pass 1, design criteria sets:
- Use capability areas from market context as evaluation criteria
- Add criteria for segments that were underrepresented
- Max 5 criteria per Discovery task

```json
{
  "query": "2-3 sentence description of ideal company for this level",
  "desired_num_results": 50,
  "criteria": [
    {"key": "core_product", "name": "Core Product", "criterion": "..."},
    {"key": "target_market", "name": "Target Market", "criterion": "..."}
  ]
}
```

**Step 2: Run Discovery tasks**

`POST /discovery_tasks` per level.

**Step 3: Poll until complete**

`GET /discovery_tasks/{task_id}` every 60 seconds until `status` is `done` or `failed`.

**Step 4: Fetch and integrate results**

`GET /discovery_tasks/{task_id}/results` — fetch all results.

Deduplicate against existing table for this level. Upload new companies to the level's existing table.

**Step 5: Present updated counts**

Show what Discovery added:

| Level | Before Discovery | New from Discovery | Total |
|-------|-----------------|-------------------|-------|
| {level} | N | +N | N |

Note any notable companies Discovery found that semantic search missed.

---

### Phase D: Competitive Enrichment

Design and run enrichment columns on the tables. Three categories of columns.

**Step 1: Select columns**

Read market context for axes and capability areas. Propose columns from each category.

See [references/enrichment-columns-vc.md](references/enrichment-columns-vc.md) for pre-built column configs.

**Category A — Positioning Axis Columns:**
- One `grade` column per positioning axis (from market context)
- Agent type: `research_reasoning`
- Customize the prompt with the specific axis poles from market_context.md

**Category B — Feature/Capability Columns:**
- One `json` column per capability area (from market context)
- Agent type: `research_pro`
- Rates each specific capability 0-3
- **Direct competitors:** full capability columns
- **Indirect competitors:** capability columns optional (user decides)
- **Adjacent/substitute:** skip capability columns (not meaningful)

**Category C — Third-Party Intelligence Columns:**
- Standard set applied to all tables:
  1. Review Sentiment (json) — G2, Capterra ratings and themes
  2. Analyst Positioning (text) — Gartner, Forrester coverage
  3. Recent Funding (json) — round, amount, investors
  4. Hiring Signals (text) — strategic hires, department focus
  5. Recent News (text) — launches, partnerships, acquisitions
  6. Target Market (select) — SMB / Mid-Market / Enterprise / All

**Step 2: Confirm with user**

Present proposed columns per table:

```
Direct competitors table — 15 columns:
  Axis: 4 columns (breadth/depth, SMB/enterprise, self-serve/sales-led, horizontal/vertical)
  Capabilities: 5 columns (one per capability area)
  Intelligence: 6 columns (reviews, analysts, funding, hiring, news, target market)

Indirect competitors table — 10 columns:
  Axis: 4 columns
  Intelligence: 6 columns

Adjacent competitors table — 10 columns:
  Axis: 4 columns
  Intelligence: 6 columns

Substitute (software-adjacent) table — 6 columns:
  Intelligence: 6 columns
```

Ask: "Does this column plan look right? Any to add or remove?"

**Step 3: Create columns**

For each table, create confirmed columns via `POST /tables/{table_id}/columns`.

**Step 4: Trigger enrichment**

For each table, run `POST /tables/{table_id}/run` scoped to the new column UUIDs.

Report total cells queued.

**Step 5: Monitor progress**

Poll each table via `GET /tables/{table_id}/data` every 60 seconds. Report completion percentage.

**Step 6: Quality spot-check**

After 50%+ completion on the direct competitors table, fetch 5 rows and display a sample:
- Are axis scores reasonable?
- Are capability ratings accurate for known companies?
- Is third-party data correct?

If quality issues: delete the column (`DELETE /tables/{id}/columns/{column_id}`), adjust the prompt, recreate, re-run.

**Step 7: Build feature comparison matrices**

Once enrichment completes for direct competitors (at minimum), build capability matrices:

```markdown
## {Capability Area}

| Company | {Capability 1} | {Capability 2} | {Capability 3} |
|---------|----------------|----------------|----------------|
| Company A | 3 (best-in-class) | 2 (good) | 0 (not offered) |
| Company B | 1 (basic) | 3 (best-in-class) | 2 (good) |

Legend: 0 = Not offered, 1 = Basic, 2 = Good, 3 = Best-in-class
```

Save to `claude-code-gtm/vc-maps/{map-slug}/feature_comparison.md`.

---

## Outputs

| Output | Path / Location |
|--------|----------------|
| Scoping document | `claude-code-gtm/vc-maps/{map-slug}/competitor_scoping.md` |
| Direct competitors table | Extruct table (ID saved in scoping doc) |
| Indirect competitors table | Extruct table (ID saved in scoping doc) |
| Adjacent competitors table | Extruct table (ID saved in scoping doc) |
| Substitute competitors table | Extruct table (ID saved in scoping doc) |
| Feature comparison | `claude-code-gtm/vc-maps/{map-slug}/feature_comparison.md` |

## Output Consumers

- `market-map-assembly` — reads enriched tables + feature comparison for final report

## When NOT to Use

- If the user just wants to define a market — use `market-definition`
- If the user just wants a report from existing tables — use `market-map-assembly`
- If the user wants to run GTM outbound campaigns — use the GTM skills (list-building, list-enrichment, etc.)
- If the user already has enriched competitor tables and just needs the report — use `market-map-assembly`
