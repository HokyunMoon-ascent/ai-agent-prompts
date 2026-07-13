<!-- v.1.0.0_cep_EN_0710.md (updated 2026-07-10) — Multiple-keyword branch version of Phase 2 (unconfirmed draft) -->

## Phase 2-2 — Multiple-Keyword Product Anchors (Category Anchors + CEP Seed Extraction) (unconfirmed)

> **Branch condition**: When the keywords entered by the user are **multiple (comma-separated)**, enter this prompt instead of Phase 2.
> If a single keyword, use the existing Phase 2 (`Product Anchors`) as-is.
> The input receives the deliverables of Phase 1-2 (preliminary basic-information research for multiple keywords).

### Differences from Phase 2 (single)

| Item      | Phase 2 (single keyword)              | Phase 2-2 (multiple keywords · this document)                                                          |
| --------- | ---------------------------------- | ------------------------------------------------------------------------------------------ |
| Input     | Single product name + basic research summary       | **Multiple keywords** + Phase 1-2 summary                                                           |
| category  | 2-4 nouns representing the entire product    | Same. However, derived **only from category-layer keywords**                                              |
| Output addition | `{ category }`                     | `{ category, cep_seeds }` — **effect/super-concept keywords are preserved as `cep_seeds` instead of being discarded**       |
| Core rule | "feature/perspective is not category" | Keep the same rule + **effect terms must NOT be deleted or abstracted; emit them as `cep_seeds`** for Phase 3·4 to consume       |

### Purpose

Extract **category anchors for vector retrieval** from multiple keywords.
However, effect/attribute/super-concept keywords are not lumped into categories and discarded, but **separated and preserved as `cep_seeds` (CEP · Nano-Intent seeds)**.
Effect keywords (e.g., `주름개선`, `피부 탄력개선`) are key clues for discovering CEPs that reveal consumer deficits.

### Core concepts

- **category**: The product category anchor used in subsequent vector retrieval. Derived **only from category-layer keywords**, and
  does not include effect/attribute/super-concept/brand/model names.
- **cep_seeds**: A list of effect/attribute/super-concept keywords that were not placed into category, **preserved verbatim**.
  Used as seeds for deficit/situation exploration in Phase 3 (CEP Insight) · Phase 4 (CEP Trigger · Nano-Intent).
  Effects are not exclusive to cosmetics; they appear in **every domain (appliances, food, services, etc.)**
  (appliances: `발열 적은` · food: `혈행개선` · services: `재고관리`).
- **Granularity-separation principle**: To satisfy both the exclusion rule "effects are not category" and the preservation rule "effects are not discarded"
  at the same time, split into the **two fields** category and cep_seeds.
- **Empty-cep_seeds allowance principle**: If the input is category/brand-centric and there are no effect/super-concept keywords at all,
  set `cep_seeds` to an **empty array `[]`**, and **do not invent** keywords that were not in the input.

### Input variables

| Variable                     | Description                          | Example                                                      |
| ---------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| `{{product_name}}`           | **Multiple keywords (comma-separated)** original input | `주름개선 화장품, 주름개선, 안티에이징, 노화방지 화장품, 피부 탄력개선` |
| `{{country}}`                | Country code                        | `kr`                                                         |
| `{{response_language}}`      | Output language (ko/en/ja)          | `ko`                                                         |
| `{{basic_research_summary}}` | Phase 1-2 result summary            | (markdown text)                                            |

### Output format

JSON object

```json
{
  "category": ["안티에이징 화장품", "기능성 화장품", "스킨케어"],
  "cep_seeds": [
    { "keyword": "주름개선", "type": "effect" },
    { "keyword": "피부 탄력개선", "type": "effect" },
    { "keyword": "안티에이징", "type": "super_concept" }
  ]
}
```

- Allowed `type` values: `effect` (effect/attribute) / `super_concept` (super-concept).
- Category-layer keywords (e.g., `주름개선 화장품`, `노화방지 화장품`) are not placed into `cep_seeds`; they are used only to derive `category`.

### Request model and parameters

Same as Phase 2.

| Parameter  | Setting                                                                                        |
| :-------- | :-------------------------------------------------------------------------------------------- |
| model     | 'gpt-5.4-nano'                                                                                |
| input     | buildMultiAnchorsPrompt({ productName, country, responseLanguage, basicResearchSummary })     |
| text      | { format: { type: 'text' } }                                                                  |
| reasoning | { effort: 'none' }                                                                            |

---

### Prompt template

```
You are generating machine-readable product anchors for vector retrieval from MULTIPLE input keywords.

Constraints:
- All strings MUST be written in {{response_language}}.

Context:
- productKeywords (comma-separated): {{product_name}}
- country: {{country}}

Visible research (Basic Research for multiple keywords, preprocessed section summary):
{{basic_research_summary}}

# Step 1 — Classify each input keyword
This layering applies to ANY product domain (cosmetics, electronics, food/supplements, appliances, services) — NOT just beauty.
Classify every comma-separated keyword into ONE layer:
- **category**: a product category — the thing being sold (cosmetics: 주름개선 화장품 · electronics: 게이밍 노트북 · food: 오메가3)
- **effect**: a functional effect/benefit/attribute consumers want, in ANY domain (cosmetics: 주름개선 · electronics: 발열 적은 · food: 혈행개선)
- **super_concept**: a broad umbrella theme (cosmetics: 안티에이징 · electronics: 고성능 · food: 건강기능식품)

# Step 2 — Build category anchors
- Derive "category" from the CATEGORY-layer keywords only (plus, if needed, the smallest common category the effects/super-concepts belong to).
- 2-4 generic/common nouns or short noun phrases (no brand/model names).
- Deduplicate and sort broad → specific.
- The categories must represent the overall product, NOT a single feature or a specific perspective.

# Step 3 — Preserve CEP seeds (do NOT discard)
- Put every "effect" and "super_concept" keyword into "cep_seeds" verbatim, each with its type.
- effect/super_concept keywords are the strongest CEP/Nano-Intent clues — NEVER drop them and NEVER put them into "category".
- Do NOT add keywords that were not in the input.
- If there are NO effect/super_concept keywords (input is only categories/brands, e.g., "생수, 삼다수, 에비앙"), set "cep_seeds" to an empty array []. Do NOT fabricate seeds.

Schema (exact):
{
  "category": ["..."],
  "cep_seeds": [ { "keyword": "...", "type": "effect" | "super_concept" } ]
}

Now output ONLY the JSON.
```
