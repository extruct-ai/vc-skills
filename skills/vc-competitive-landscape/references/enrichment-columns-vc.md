# VC Market Map — Pre-built Enrichment Column Configs

Column configs organized by category. Copy, customize the prompts, and pass to `POST /tables/{table_id}/columns`.

---

## Category A: Feature/Capability Columns

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

## Category B: Intelligence Columns

### Recent Funding

```json
{
  "kind": "agent",
  "name": "Recent Funding",
  "key": "recent_funding",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find the most recent funding information for {input}. Use credible sources such as Crunchbase, PitchBook, or press releases. Search in the company's primary language (e.g., if the company is Brazilian, search in Portuguese). Return a JSON object with: last_round (e.g. 'Series B'), amount (e.g. '$25M' or 'R$10M'), date (e.g. '2024-06'), lead_investor (name), total_raised (e.g. '$45M'). If bootstrapped, set last_round to 'Bootstrapped'. If unknown, use 'Unknown'.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "last_round": {"type": "string"},
        "amount": {"type": "string"},
        "date": {"type": "string"},
        "lead_investor": {"type": "string"},
        "total_raised": {"type": "string"}
      }
    }
  }
}
```

### Parent Ownership

```json
{
  "kind": "agent",
  "name": "Parent Ownership",
  "key": "parent_ownership",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Determine the ownership structure of {input}. Search in the company's primary language. Is it: independent/founder-led, PE-backed (which firm?), a subsidiary of a larger company (which parent?), publicly traded (which exchange?), or acquired (by whom, when)? Return a JSON object with: status (one of: 'Independent', 'PE-backed', 'Subsidiary', 'Public', 'Acquired'), parent_or_investor (name of parent company, PE firm, or acquirer — 'N/A' if independent), details (1 sentence with context). Use Crunchbase, PitchBook, LinkedIn, or press releases.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "status": {"type": "string"},
        "parent_or_investor": {"type": "string"},
        "details": {"type": "string"}
      }
    }
  }
}
```

### Key Clients

```json
{
  "kind": "agent",
  "name": "Key Clients",
  "key": "key_clients",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find notable clients or customers of {input}. Search the company's website (case studies, logos, testimonials), press releases, and review sites. Search in the company's primary language. Return a JSON object with: clients (array of up to 10 company names), source (where you found this — e.g. 'website logos', 'case study', 'G2 review', 'press release'). If no clients found, return empty array and source 'Not found'.",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "clients": {"type": "array", "items": {"type": "string"}},
        "source": {"type": "string"}
      }
    }
  }
}
```

### Review Sentiment

```json
{
  "kind": "agent",
  "name": "Review Sentiment",
  "key": "review_sentiment",
  "value": {
    "agent_type": "research_pro",
    "prompt": "Find reviews of {input} on G2, Capterra, TrustRadius, Product Hunt, or local review sites relevant to the company's market. Return a JSON object with: overall_rating (number out of 5, or 0 if unknown), review_count (number, or 0 if unknown), top_praise (string — most common positive theme, or 'N/A'), top_complaint (string — most common negative theme, or 'N/A'), sources (array of site names where data was found, empty array if none).",
    "output_format": "json",
    "output_schema": {
      "type": "object",
      "properties": {
        "overall_rating": {"type": "number"},
        "review_count": {"type": "number"},
        "top_praise": {"type": "string"},
        "top_complaint": {"type": "string"},
        "sources": {"type": "array", "items": {"type": "string"}}
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
    "prompt": "Find the most significant news about {input} from the last 12 months. Search in the company's primary language. Focus on: product launches, partnerships, acquisitions, leadership changes, or market expansion. Return 2-3 bullet points with dates. If no significant news found, return 'No significant recent news found'.",
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

| Map complexity | Capability columns | Intelligence columns | Total |
|---------------|-------------------|---------------------|-------|
| Quick scan | 0 | 4-5 | 4-5 |
| Standard map | 0 | 7 | 7 |
| Deep analysis | 3-5 | 7 | 10-12 |

**Standard 7 intelligence columns:** Recent Funding, Parent Ownership, Key Clients, Review Sentiment, Hiring Signals, Recent News, Target Market.

**Rules:**
- All tables get the standard intelligence columns
- Capability columns are optional, only for direct competitors in deep analysis mode
