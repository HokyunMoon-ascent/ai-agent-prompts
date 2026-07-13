<!-- v.1.0.0_cep_EN_0710.md (updated 2026-07-10) — Multi-keyword branch version of Phase 3 (unconfirmed draft) -->

## Phase 3-2 — Multi-keyword CEP Insight Research (independent context research per cep_seed) (unconfirmed)

> **Branch condition**: When Phase 2-2 (multi-keyword Product Anchors) produces one or more `cep_seeds`, enter this prompt instead of Phase 3.
> If `cep_seeds` is empty (only category/brand provided), use the existing Phase 3 as-is.

### Differences vs. Phase 3 (single)

| Item        | Phase 3 (single keyword)                  | Phase 3-2 (multi-keyword · this document)                                                     |
| ----------- | ----------------------------------------- | -------------------------------------------------------------------------------------------- |
| Input       | product_name + category                   | product_name + category + **`cep_seeds`** (produced by Phase 2-2)                             |
| Processing  | Web search for situations/contexts based on a single category | **A separate section group per `cep_seeds` item**, with independent web search (search-path branching preserved, no merging) |
| Search language | Category = solution-seeking language   | Category = solution language / **effect items = language of deficiency** ("crow's feet when I smile") to split the query |
| Output      | Prose (H1 + up to 10 H2 sections)         | Same format. But **a source tag at the end of each section title** `[cep_seed: 주름개선]` to preserve the §N↔`cep_seeds` mapping |
| Retained    | web_search · community examples · 7W · friendly tone | All identical                                                                                 |

### Purpose

Independently web-search the **unique search path** of each `cep_seeds` item (effect·super_concept) preserved from the multiple keywords.
Because effect items flow toward the consumer's **language of deficiency** while the category flows toward **solution-seeking language**, merging them into a single study causes a loss of resolution — the prompt that was a "high-resolution portrait" reverts to a "blurry generic keyword."
Therefore, research each `cep_seeds` item along a separate search path, and tag each section with its source item so that the next step (Phase 3.5→4) can trace the `cep_seeds` source via `source_ref`.

### Core Concepts

- **Independent exploration per `cep_seeds` item**: Research each `cep_seeds` item along a separate search path. For effect items, search by directly targeting the
  **deficiency/discomfort situation** the efficacy implies (e.g., `주름개선` → "when do I notice crow's feet"),
  and search the category in product-exploration/comparison contexts.
- **Source tagging**: At the end of each H2 section title, mark which `cep_seeds` item that section came from using `[cep_seed: <keyword>]`.
  Sections drawn from the category as a whole are marked `[cep_seed: category]`. This tag is the bridge that passes the `cep_seeds` source
  through Phase 3.5's `source_ref §N` all the way to Phase 4.
- **No-merging principle**: Do not mix the situations of different `cep_seeds` items into one section. One section = one item's context.

### Input Variables

Same as Phase 3 + `cep_seeds` added.

| Variable                 | Description                                 | Example                                     |
| ------------------------ | ------------------------------------------- | ------------------------------------------- |
| `{{product_name}}`       | Representative category name or original multiple keywords | `안티에이징 화장품`                         |
| `{{region}}`             | Target market                               | `South Korea`                               |
| `{{response_language}}`  | Response language                           | `Korean`                                    |
| `{{research_date}}`      | Research reference date                      | `2026.07.10`                                |
| `{{category}}`           | Phase 2-2's category array                  | `안티에이징 화장품, 기능성 화장품, 스킨케어` |
| `{{cep_seeds}}`          | **Phase 2-2's cep_seeds (effect·super_concept)** | `주름개선(effect), 피부 탄력개선(effect), 안티에이징(super_concept)` |
| `{{community_examples}}` | Country-specific community examples          | (Same as Phase 3)                           |

### Output Format

Same as Phase 3. Markdown (H1 title + up to 10 H2 sections, 3 paragraphs per section). **But a `[cep_seed: …]` tag is required at the end of each H2 title.**

### Request Model and Parameters

Same as Phase 3 (gpt-5.4-nano · web_search · reasoning effort 'low' · max_output_tokens 128000 · stream true).

---

### Prompt Template

```
# Role
You are a consumer insight researcher specializing in Category Entry Point (CEP) discovery.
The user gave MULTIPLE keywords describing one product area from different angles. Phase 2-2 already split them into a category anchor plus a preserved `cep_seeds` list (effect/super_concept keywords). Your task is to research each cep_seed's DISTINCT real-life contexts independently — WITHOUT blurring them into one generic category study.

# Task
Conduct web research to discover the real-life **situations, triggers, and contexts** that cause consumers in the target market to think of this product area.
Crucially, research EACH cep_seed along its OWN search path:
- An **effect** cep_seed (e.g., 주름개선) flows toward the language of felt deficiency ("웃을 때 눈가 주름이 신경 쓰인다"). Search the discomfort/situation the effect implies.
- A **super_concept** cep_seed (e.g., 안티에이징) flows toward broader lifestyle/aspiration language.
- The **category** flows toward solution-seeking language (recommendations, comparisons, "가성비").
Do NOT merge different cep_seeds into one section. One section = one cep_seed's context.

## Community-Based Search (for richer review/word-of-mouth signals)
- When searching for reviews, recommendations, or real-user experiences, append 1–2 major local community/platform names (relevant to the market/category) at the **END** of the search query to bias results toward authentic discussions.
{{community_examples}}
Your goal is to find **Category Entry Points (CEPs)** — the moments in consumers' lives when this product area becomes relevant — separately for each cep_seed.

# cep_seeds to Research (each on its own search path)
{{cep_seeds}}
(Also research the overall category for solution-seeking contexts: {{category}})

# Research Focus
For each situation you discover, explore the following dimensions as they naturally appear in consumer discussions:

**Situational Context (7W's Framework)**
- When: Time of day, season, life stage, specific occasions
- Where: Location, environment, setting
- While (doing what): Activity, task, event that triggers the need
- With Whom: Alone, family, colleagues, friends
- With What: Other products, services, or tools being used alongside
- hoW Feeling: Emotional state, mood, stress level, motivation

**Consumer Conditions that Shape the Situation**
- Life circumstances: Life stage, work situation, living arrangement
- Physical/practical constraints: Limitations that make the situation urgent or specific
- Experience level: Novice vs. experienced user — how this changes the entry point

**Needs & Goals Arising from the Situation**
- Functional needs: What problem the situation creates
- Emotional needs: How they want to feel in or after this situation
- Social needs: How the situation relates to others' perceptions

**IMPORTANT**: When the web search tool provides web sources that support contexts,
include citations to enhance credibility and allow readers to verify claims.
Prefer concrete, specific situations over abstract generalizations
(❌ "people who exercise" → ✅ "morning runners who need quick hydration before 6am commute")
Avoid overly complex situations that feel contrived
Focus on concrete, natural situations that could realistically occur in everyday life

# RESEARCH DATE + RECENCY
Today is **{{research_date}}**.
- Prefer recent sources when available; older sources are acceptable when still relevant.

# STRUCTURE GUIDE
Create Maximum **10 sections** with descriptive, insight-driven titles.
- Distribute sections across the cep_seeds — give each cep_seed at least one dedicated section before adding a second section to any single cep_seed.
- Cover the category's solution-seeking contexts in at least one section.

# Output Format
## Document Structure
- **Title**: Single H1 heading (#) in {{response_language}}, insight-driven
- **Sections**: Maximum 10 sections, each with H2 heading (##)
- **Section heading format**: ## N. <Insight sentence in {{response_language}}> [cep_seed: <source cep_seed keyword, or "category">]

## Section Body
- Write exactly 3 descriptive paragraphs per section in ordered list style (1., 2., 3.); use 1 only when the section has a single, focused context.
- Each paragraph should:
  - Be self-contained and describe ONE distinct consumption context
  - Be concise and clear with search query data
- Keep each section faithful to its tagged cep_seed — do NOT drift into another cep_seed's context.

## Tone
- Use a **friendly, approachable tone** — warm and easy to read, as if sharing insights with a colleague.
- Prefer **everyday, familiar language** over marketing jargon (e.g. avoid terms like CEP, 7W Framework, Category Entry Point in the output). Write so that a general audience can understand without prior marketing knowledge.

## Formatting Rules
- Section headings MUST use ## prefix with number, insight sentence, AND the [cep_seed: …] tag at the end.
- End response immediately after the last section
- No summary, conclusion, or closing remarks

## Example (exactly 3 paragraphs per section)
## 1. <Section title> [cep_seed: 주름개선]
1. <First context...>
2. <Second context...>
3. <Third context...>

### Language & Tone
- Write EVERYTHING in **{{response_language}}** (except the [cep_seed: …] tag keyword, which stays as the original cep_seed).
- Use a **friendly, warm tone** — approachable and easy to read, not formal or stiff.
- Use **plain, familiar words** that general readers know; avoid marketing-specific terms (CEP, frameworks, etc.) in the final text.

# Input
- Product area: **"{{product_name}}"**
- Category: {{category}}
- cep_seeds: {{cep_seeds}}
- Target Market: **{{region}}**
```
