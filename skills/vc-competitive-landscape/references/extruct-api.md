# Extruct API Reference — Consolidated

Base URL: `https://api.extruct.ai/v1`
Auth: `Authorization: Bearer {EXTRUCT_API_TOKEN}`

Official docs: https://www.extruct.ai/docs/api-reference/introduction

---

## Semantic Search

`GET /v1/companies/search`

| Param | Type | Description |
|-------|------|-------------|
| `q` | string | Natural language query describing target companies |
| `filters` | JSON string | Include/exclude/range filters (see below) |
| `limit` | int | Max results. Default 100, max 200 |
| `offset` | int | For pagination |

Response fields: `name`, `domain`, `short_description`, `founding_year`, `employee_count`, `hq_country`, `hq_city`, `relevance_score`

---

## Lookalike Search

`GET /v1/companies/{identifier}/similar`

`identifier` = domain or company UUID.

| Param | Type | Description |
|-------|------|-------------|
| `filters` | JSON string | Include/exclude/range filters |
| `limit` | int | Max results. Default 100, max 200 |
| `offset` | int | For pagination |

Same response fields as semantic search.

---

## Discovery API (Deep Search)

`POST /v1/discovery_tasks`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `query` | string | yes | 2-3 sentence description of ideal company |
| `desired_num_results` | int | no | 1-250. Default 50 for first pass |
| `criteria` | array | no | Up to 5 evaluation criteria |
| `table` | object | no | Auto-import to a table |

Criteria format:
```json
{"key": "has_api", "name": "API Available", "criterion": "Company offers a public API for data integration"}
```

Poll: `GET /v1/discovery_tasks/{task_id}` — every 60 seconds
Status: `created` → `in_progress` → `done` | `failed`

Results: `GET /v1/discovery_tasks/{task_id}/results?limit=100&offset=0`

Result fields: `company_name`, `company_website`, `company_description`, `relevance` (0-100), `founding_year`, `source`, `scores` (per-criterion grade 1-5 with explanation)

---

## Tables

### Create Table

`POST /v1/tables`

```json
{
  "name": "Table Name",
  "kind": "company",
  "column_configs": [
    {"kind": "input", "name": "Domain", "key": "input"}
  ]
}
```

### Upload Rows

`POST /v1/tables/{table_id}/rows`

```json
{
  "rows": [
    {"data": {"input": "domain.com"}},
    {"data": {"input": "another.com"}}
  ],
  "run": true
}
```

Upload in batches of 50. Add 0.5s delay between batches. Set `"run": true` to trigger agent columns on upload.

### Get Table Metadata

`GET /v1/tables/{table_id}`

Returns table config including columns, child_relationships.

### Get Table Data

`GET /v1/tables/{table_id}/data`

Returns all rows with cell values.

---

## Columns & Enrichment

### Add Columns

`POST /v1/tables/{table_id}/columns`

```json
{
  "column_configs": [
    {
      "kind": "agent",
      "name": "Column Display Name",
      "key": "column_key_snake_case",
      "value": {
        "agent_type": "research_pro",
        "prompt": "Your prompt using {input} as the row value",
        "output_format": "text"
      }
    }
  ]
}
```

### Agent Types

| Type | Use case |
|------|----------|
| `research_pro` | Deep web research with sources. Default choice. |
| `research` | Lighter web research. Speed over depth. |
| `research_reasoning` | Research + chain-of-thought reasoning. Nuanced analysis. |
| `llm` | No web search. Classification from existing profile data. |
| `linkedin` | LinkedIn-specific research. People, roles, org structure. |

### Output Formats

**Simple:** `text`, `number`, `money`, `url`, `email`, `phone`, `date`, `grade` (1-5)

**With labels:** `select`, `multiselect` — requires `"labels": ["Option1", "Option2"]`

**Structured:** `json` — requires `"output_schema": { ... }`

### Trigger Enrichment

`POST /v1/tables/{table_id}/run`

Run specific columns: `{"columns": ["column-uuid-1", "column-uuid-2"]}`
Run all pending: `{}`

### Delete Column

`DELETE /v1/tables/{table_id}/columns/{column_id}`

---

## Search Filters

Pass as JSON string in `filters` param for semantic and lookalike searches.

```json
{
  "include": {"size": ["11-50", "51-200"], "country": ["United States"]},
  "exclude": {"city": ["San Francisco"]},
  "range": {"founded": {"min": 2015, "max": 2024}}
}
```

Size buckets: `1-10`, `11-50`, `51-200`, `201-500`, `501-1000`, `1001-5000`, `5001-10000`, `10001+`

Filter fields: `country`, `city`, `size` (in include/exclude), `founded` (in range).
