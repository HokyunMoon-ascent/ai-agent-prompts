<!-- v.1.0.0_cep_EN_0508.md (updated 2026-05-08) -->

## Phase 2 — Product Anchors (Category Anchor Extraction)

### Purpose

Based on the results of the basic information preliminary research, extract **category keywords for vector retrieval**.  
These are used as product category context in the keyword searches of subsequent phases.

### Input Variables

| Variable                     | Description                                | Example           |
| ---------------------------- | ------------------------------------------ | ----------------- |
| `{{product_name}}`           | Product name                               | `갤럭시 S25`      |
| `{{country}}`                | Country code                               | `kr`              |
| `{{response_language}}`      | Output language (ko/en/ja)                 | `ko`              |
| `{{basic_research_summary}}` | Summary of the basic preliminary research  | (markdown text)   |

### Output Format

JSON object

```json
{
  "category": ["스마트폰", "안드로이드폰", "플래그십폰"]
}
```

### Request Model and Parameters

| Parameter | Value                                                                                        |
| :-------- | :------------------------------------------------------------------------------------------ |
| model     | 'gpt-5.4-nano'                                                                              |
| input     | buildProductAnchorsPrompt({ productName, country, responseLanguage, basicResearchSummary }) |
| text      | { format: { type: 'text' } }                                                                |
| reasoning | { effort: 'none' }                                                                          |

---

### Prompt Template

```
You are generating machine-readable product anchors for vector retrieval.

Constraints:
- All strings MUST be written in {{response_language}}.

Context:
- productName: {{product_name}}
- country: {{country}}

Visible research (Basic Research, preprocessed section summary):
{{basic_research_summary}}

Schema (exact):
{
  "category": ["..."]
}

Rules:
- category: 2-4 generic/common nouns or short noun phrases (no brand/model names).
- Deduplicate and sort from broad → specific when possible.
- The categories must represent the overall product, not a single feature or a specific perspective.

Now output ONLY the JSON.
```
