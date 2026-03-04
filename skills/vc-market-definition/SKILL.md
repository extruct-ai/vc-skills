---
name: market-definition
description: >
  Define the market context for a VC market map. Two entry modes: start from a
  specific company (reverse-engineer its market) or from a market category
  description (top-down). Outputs a market definition document with positioning
  axes, capability areas, and buyer personas. Triggers on: "market map",
  "map a market", "competitive landscape", "market mapping", "vc market map",
  "analyze market", "define market", "market definition".
---

# Market Definition

Define the market context that drives all downstream skills. Two entry modes: start from a company or from a market category. Outputs a structured market definition with positioning axes, capability areas, and buyer personas.

## Environment

| Variable | Service |
|----------|---------|
| `EXTRUCT_API_TOKEN` | Extruct API |

Base URL: `https://api.extruct.ai/v1`

## Official API Reference

- https://www.extruct.ai/docs/api-reference/introduction

## Workflow

### Step 1: Determine entry mode

Ask the user:

| Mode | When to use | What's needed |
|------|-------------|--------------|
| **A: Company** | Start from a specific company, map its competitive landscape | Company domain |
| **B: Category** | Start from a market category, map all players | Market description (2-3 sentences) |

### Step 2: Gather market context

**Mode A — Company entry:**

1. Look up the company via `GET /companies/search?q={domain}&limit=1` to get its profile (name, description, employee count, founding year).
2. Run 1-2 broad Extruct searches to discover similar companies and understand the market landscape.
3. **Draft the market description yourself** (2-3 sentences) based on the company profile, search results, and any context the user already provided (e.g., "AI legal SaaS in Brazil"). Do NOT ask the user to write it — present your draft for confirmation.
4. Ask: "Are there any well-known competitors you can name?" (These become seed companies for lookalike search later.) If the user says no, use companies found in search results as seeds.

**Mode B — Category entry:**

1. If the user already described the market (even briefly), use that as the basis. Only ask for more detail if the description is too vague to build axes from.
2. Ask: "Can you name 2-3 example companies in this market?" (Optional, but helps ground the definition.)

**In both modes:**
- Infer geographic scope from context (e.g., "Brazil" was mentioned → Brazil). Only ask if genuinely ambiguous.
- Default company stage to "all stages" unless the user specified otherwise.
- Minimize questions. Draft proposals and confirm, don't ask open-ended questions the skill can answer itself.

### Step 3: Propose full market definition in one shot

Read [references/positioning-axes.md](references/positioning-axes.md) for the standard 5 axes.

**Present ALL of the following together in a single message** — axes, capabilities, and personas. Do NOT split them into separate steps with separate confirmations. The user should review and approve the full definition at once.

**Positioning axes (3-5):**

Based on the market description and company profiles found, propose 3-5 axes. For each:

```
Axis: [Name]
Left pole: [Label] — [description]
Right pole: [Label] — [description]
Why it matters: [1 sentence]
```

Include **SMB vs Enterprise** by default. Add custom axes specific to the market (e.g., "AI-native vs Traditional", "Open source vs Proprietary").

**Capability areas (5-8):**

Propose the functional categories buyers evaluate. For each:

```
Area: [Name]
Capabilities:
- [Specific capability 1]
- [Specific capability 2]
- [Specific capability 3]
Why buyers care: [1 sentence]
```

Use buyer language, not vendor architecture.

**Buyer personas (2-4):**

```
Persona: [Title/Role]
Cares about: [Key priorities]
Evaluates on: [What matters when choosing a product]
Preferred axes: [Which positioning axes matter most to this buyer]
```

**Present everything together, then ask once:** "Does this market definition look right? Anything to add, remove, or change?"

### Step 4: Save market context

Save to:

```
claude-code-gtm/vc-maps/{map-slug}/market_context.md
```

Create the directory if it doesn't exist. The `{map-slug}` is derived from the market name (lowercase, hyphenated).

**Format:**

```markdown
# Market Context: {Market Name}

## Market Description
{2-3 sentence description}

## Scope
- Geographic: {scope}
- Company stage: {stage focus}
- Focal company: {company name or "N/A"}

## Seed Companies
{List of known companies from user input, one per line with domain}

## Positioning Axes

### {Axis 1 Name}
- Left: {label} — {description}
- Right: {label} — {description}
- Why: {1 sentence}

### {Axis 2 Name}
...

## Capability Areas

### {Area 1 Name}
- {Capability 1}
- {Capability 2}
- {Capability 3}
Why: {1 sentence}

### {Area 2 Name}
...

## Buyer Personas

### {Persona 1 Title}
Cares about: {priorities}
Evaluates on: {criteria}
Preferred axes: {which axes}

### {Persona 2 Title}
...
```

## Handling Knowledge Gaps

If you need deeper market data that you don't have:
1. First, try running a broad Extruct search (`GET /companies/search`) to discover companies in the space and learn from their descriptions.
2. If that's insufficient, ask the user: "I need more context on [specific aspect]. Can you provide additional research or context?"
3. Never guess or fabricate market data. If uncertain, say so and ask.

## Output Consumers

The market context is consumed by:
- `competitive-landscape` — reads axes (for enrichment columns), capability areas (for feature comparison), seed companies (for lookalike search)
- `market-map-assembly` — reads everything (for report structure)

## When NOT to Use

- If the user already has a `market_context.md` for this market — update it, don't recreate
- If the user just wants to find companies — use `competitive-landscape` directly
- If the user wants a finished report — run the full pipeline (this skill first)
