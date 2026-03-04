# VC Market Map — Pre-built Enrichment Column Configs

Column configs organized by category. Copy, customize the prompts, and pass to `POST /tables/{table_id}/columns`.

---

## Category A: Positioning Axis Columns

### Breadth vs Depth

```json
{
  "kind": "agent",
  "name": "Axis: Breadth vs Depth",
  "key": "axis_breadth_depth",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Evaluate where {input} sits on the spectrum from 'Point Solution' to 'Full Suite'. Consider: how many distinct product capabilities do they offer? Do they focus on one thing or cover a broad workflow? 1 = pure point solution (single function), 3 = multi-feature with clear core, 5 = all-in-one platform. Return ONLY a number 1-5.",
    "output_format": "grade"
  }
}
```

### SMB vs Enterprise

```json
{
  "kind": "agent",
  "name": "Axis: SMB vs Enterprise",
  "key": "axis_smb_enterprise",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Evaluate where {input} sits on the spectrum from SMB to Enterprise. Consider: pricing (free/<$50 = SMB, $50K+ ACV = enterprise), sales model (self-serve vs sales-led), product complexity, and customer logos. 1 = pure SMB/freelancer, 3 = mid-market, 5 = pure enterprise. Return ONLY a number 1-5.",
    "output_format": "grade"
  }
}
```

### Self-Serve vs Sales-Led

```json
{
  "kind": "agent",
  "name": "Axis: Self-Serve vs Sales-Led",
  "key": "axis_selfserve_salesled",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Evaluate the go-to-market approach of {input}. Can users sign up and use the product without talking to sales? Is there a free trial or freemium? Or must users request a demo / go through procurement? 1 = fully self-serve, 3 = hybrid, 5 = fully sales-led. Return ONLY a number 1-5.",
    "output_format": "grade"
  }
}
```

### Simple vs Powerful

```json
{
  "kind": "agent",
  "name": "Axis: Simple vs Powerful",
  "key": "axis_simple_powerful",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Evaluate the product complexity of {input}. Is it easy to set up in minutes with opinionated defaults, or highly configurable with a steep learning curve? Consider onboarding time, documentation depth, and target user technical level. 1 = dead simple, 3 = moderate complexity, 5 = highly technical and configurable. Return ONLY a number 1-5.",
    "output_format": "grade"
  }
}
```

### Horizontal vs Vertical

```json
{
  "kind": "agent",
  "name": "Axis: Horizontal vs Vertical",
  "key": "axis_horizontal_vertical",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Evaluate whether {input} is a general-purpose (horizontal) or industry-specific (vertical) product. Does it serve all industries equally, or is it built for specific verticals with specialized features? 1 = fully horizontal, 3 = multi-vertical, 5 = single-industry vertical. Return ONLY a number 1-5.",
    "output_format": "grade"
  }
}
```

---

## Category B: Feature/Capability Columns

### Template — Capability Area Rating

Customize `{AREA_NAME}` and the capability list per market.

```json
{
  "kind": "agent",
  "name": "Capabilities: {AREA_NAME}",
  "key": "cap_{area_key}",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Evaluate the capabilities of {input} in the area of {AREA_NAME}. For each capability below, rate: 0 = not offered, 1 = basic, 2 = good, 3 = best-in-class. If you cannot determine, use -1.\n\nCapabilities:\n- {Capability 1}\n- {Capability 2}\n- {Capability 3}\n- {Capability 4}\n\nReturn a JSON object with each capability as key and rating as value.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "{capability_1}": {"type": "number"},
        "{capability_2}": {"type": "number"},
        "{capability_3}": {"type": "number"},
        "{capability_4}": {"type": "number"}
      }
    }
  }
}
```

---

## Category C: Third-Party Intelligence Columns

### Review Sentiment

```json
{
  "kind": "agent",
  "name": "Review Sentiment",
  "key": "review_sentiment",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find reviews of {input} on G2, Capterra, TrustRadius, or Product Hunt. Return a JSON object with: overall_rating (number out of 5, or null), review_count (number, or null), top_praise (string — most common positive theme), top_complaint (string — most common negative theme), sources (array of site names where data was found). If no reviews found, return null for all fields.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "overall_rating": {"type": ["number", "null"]},
        "review_count": {"type": ["number", "null"]},
        "top_praise": {"type": ["string", "null"]},
        "top_complaint": {"type": ["string", "null"]},
        "sources": {"type": "array", "items": {"type": "string"}}
      }
    }
  }
}
```

### Analyst Positioning

```json
{
  "kind": "agent",
  "name": "Analyst Positioning",
  "key": "analyst_positioning",
  "value": {
    "agent_type": "research_pro",
    "prompt": "How do industry analysts (Gartner, Forrester, IDC, CB Insights) position {input}? Is it mentioned in any Magic Quadrant, Wave, MarketScape, or market map? What category and position? If no analyst coverage found, return 'No analyst coverage found'.",
    "output_format": "text"
  }
}
```

### Recent Funding

```json
{
  "kind": "agent",
  "name": "Recent Funding",
  "key": "recent_funding",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find the most recent funding information for {input}. Return a JSON object with: last_round (e.g. 'Series B'), amount (e.g. '$25M'), date (e.g. '2024-06'), lead_investor (name), total_raised (e.g. '$45M'). If bootstrapped, set last_round to 'Bootstrapped'. If public, set last_round to 'Public'. If unknown, return null values.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "last_round": {"type": ["string", "null"]},
        "amount": {"type": ["string", "null"]},
        "date": {"type": ["string", "null"]},
        "lead_investor": {"type": ["string", "null"]},
        "total_raised": {"type": ["string", "null"]}
      }
    }
  }
}
```

### Hiring Signals

```json
{
  "kind": "agent",
  "name": "Hiring Signals",
  "key": "hiring_signals",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Analyze current job postings from {input}. What departments are they hiring for most aggressively? Any strategic hires (e.g., VP of new market, Head of Enterprise, first ML engineer)? What do the job postings reveal about their product roadmap or strategic direction? Summarize in 2-3 sentences. If no job postings found, return 'No current job postings found'.",
    "output_format": "text"
  }
}
```

### Recent News

```json
{
  "kind": "agent",
  "name": "Recent News",
  "key": "recent_news",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find the most significant news about {input} from the last 12 months. Focus on: product launches, partnerships, acquisitions, leadership changes, or market expansion. Return 2-3 bullet points with dates. If no significant news found, return 'No significant recent news found'.",
    "output_format": "text"
  }
}
```

### Target Market Segment

```json
{
  "kind": "agent",
  "name": "Target Market",
  "key": "target_market",
  "value": {
    "agent_type": "research_reasoning",
    "prompt": "Based on the pricing, sales model, customer logos, and positioning of {input}, who is their primary target market? Return ONLY one label.",
    "output_format": "select",
    "labels": ["SMB", "Mid-Market", "Enterprise", "All Segments"]
  }
}
```

---

## Column Budget Guidance

| Map complexity | Axis columns | Capability columns | Intelligence columns | Total |
|---------------|-------------|-------------------|---------------------|-------|
| Quick scan | 2-3 | 0 | 3-4 | 5-7 |
| Standard map | 3-4 | 3-5 | 5-6 | 11-15 |
| Deep analysis | 4-5 | 5-8 | 6 | 15-19 |

**Rules:**
- Direct competitors get ALL columns (axis + capability + intelligence)
- Indirect competitors get axis + intelligence, capability columns optional
- Adjacent competitors get axis + intelligence (skip capability)
- Substitute solutions (software-adjacent) get intelligence only
