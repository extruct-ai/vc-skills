# Competitor Scoping Framework

## The 4 Levels

### Level 1: Direct Competitors

**Definition:** Products that solve the same problem for the same users in the same way.

**Identification signals:**
- Appears in your deals and customer comparisons
- Listed on the same review site categories (G2, Capterra)
- Customers actively evaluate them against the focal company
- Similar pricing model and target segment

**Search query strategy — describe the PRODUCT, not the problem:**
- Template: "Companies that offer [specific product type] for [target user] to [specific function]"
- Be specific about the product category and core capability
- Include the target user / buyer persona

**Examples:**
- Market: AI code review → "Companies that offer AI-powered code review tools for engineering teams"
- Market: Sales engagement → "Companies that build sales engagement platforms for B2B outbound sales teams"
- Market: Data observability → "Companies providing data quality monitoring and observability for data engineering teams"

**Search methods:**
- Semantic search: 2-3 queries from different product angles
- Lookalike: From seed company (most effective here)
- Discovery: With criteria matching core capabilities

---

### Level 2: Indirect Competitors

**Definition:** Products that solve the same problem but with a fundamentally different approach.

**Identification signals:**
- Addresses the same user need or workflow
- Uses a different technology, methodology, or delivery model
- Often not recognized as a "competitor" in traditional sense
- Customers sometimes switch from this to the direct category (or vice versa)

**Search query strategy — describe the PROBLEM, not the product:**
- Template: "Companies that help [target user] solve [problem] using [different approach]"
- Write one query per distinct approach
- Focus on the workflow and pain point, not the product category

**Examples:**
- Market: AI code review → "Companies that help engineering teams maintain code quality through static analysis and linting" / "Developer education and training platforms for code quality"
- Market: Sales engagement → "Companies that help sales teams manage outreach through CRM automation" / "Email marketing platforms used by small sales teams for prospecting"
- Market: Data observability → "Companies offering data testing frameworks for dbt and data pipelines" / "Business intelligence tools with built-in data quality features"

**Search methods:**
- Semantic search: 2-3 queries, each for a different approach
- Discovery: Criteria focused on problem overlap, NOT product similarity

---

### Level 3: Adjacent Competitors

**Definition:** Products that don't compete today but could expand into the space.

**Three sub-categories:**

**A. Similar technology, different use case**
- Companies using the same core tech (AI, APIs, data processing) but for a different purpose
- Template: "Companies using [core technology] for [adjacent use case]"

**B. Same customers, different product**
- Companies selling to the same buyer persona but a different product
- Template: "Companies selling [different product category] to [same buyer persona]"

**C. Platform expansion risk**
- Larger platforms that could add the focal product's functionality as a feature
- Template: "Platforms for [broader category] that could add [specific capability]"

**Identification signals:**
- Overlapping customer base or technology stack
- Recent product launches moving toward the focal market
- Hiring for roles in the focal product area
- Acquisitions of companies in or near the focal market

**Search methods:**
- Semantic search: 1 query per sub-category
- Lookalike: From direct competitors (finds similar-profile companies in adjacent spaces)

---

### Level 4: Substitute Solutions

**Definition:** Entirely different ways users solve the underlying need — often non-software.

**Common substitute categories:**
1. **Hiring people** — Adding headcount instead of buying software
2. **General-purpose tools** — Excel, Google Sheets, email, Notion
3. **Outsourcing** — Agencies, consultants, managed services
4. **Manual processes** — Human review, paper-based, phone calls
5. **Non-consumption** — Ignoring the problem / living with the pain

**Handling:**
- Most substitutes are NOT software companies — they won't appear in Extruct
- Write a **narrative section** based on user input and Claude's reasoning
- Only search for **software-adjacent substitutes** (general-purpose tools that subsume the need)

**Search query strategy (software-adjacent only):**
- Template: "General-purpose [tool type] that includes [relevant capability] as a feature"
- Example: "Project management platforms with built-in code review features"

---

## Coverage Assessment After Pass 1

After running semantic + lookalike searches, assess coverage per level:

| Signal | Sufficient | Thin | Gaps |
|--------|-----------|------|------|
| Company count | 20+ | <10 | <5 |
| Recognizable names | Known players present | Few recognizable | Key players missing |
| Query overlap | Multiple queries return same companies | Little overlap | No overlap |
| Segment coverage | All sub-segments represented | Some segments missing | Major segments absent |

**Decision: when to run Discovery (Pass 2)**
- Sufficient → Skip Discovery, proceed to enrichment
- Thin → Run Discovery with broad criteria
- Gaps → Run Discovery with criteria targeting the missing segment
