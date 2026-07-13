<!-- v.1.0.0_cep_EN_0710.md (updated 2026-07-10) — Multi-keyword branch version of Phase 1 (tentative draft) -->

## Phase 1-2 — Multi-keyword Basic Information Preliminary Research (Tentative)

> **Branch condition**: When the keywords entered by the user are **multiple (comma-separated)**, enter this prompt instead of Phase 1.
> For a single keyword, use the existing Phase 1 (`Basic Information Preliminary Research`) as-is.

### Differences from Phase 1 (single)

| Item        | Phase 1 (single keyword)          | Phase 1-2 (multiple keywords · this document)                                          |
| ----------- | --------------------------------- | ------------------------------------------------------------------------------------- |
| Input       | Single product/category           | **Comma-separated multiple keywords** (mixed layers: category, effect, super-concept)  |
| Handling principle | Research the one target as-is | **Classify keyword layers → high-resolution research on one representative category + separately preserve effect/deficiency keywords** |
| Output additions | —                            | `## Keyword Composition Analysis` + `## Consumer Deficiency Signals` sections (keeping effect keywords intact rather than blurring them) |
| Retained    | web_search · source priority · language | All identical                                                                    |

### Purpose

When multiple keywords of differing nature come in, classify them by layer **rather than merging them into one blurry lump**.
Among them, pick **one representative product category** and research its basic information at high resolution, while the effect/deficiency keywords—being
the core clues for CEP discovery—are **preserved as separate signals** and handed off to the next step (Phase 2-2).

### Core Concepts

- **Keyword layer (Information Layer)** — **applied commonly across ALL domains (cosmetics, appliances, food, services, etc.)**:
  - **Category** — the product group being sold (cosmetics: `주름개선 화장품` · appliances: `게이밍 노트북` · food: `오메가3`)
  - **Effect/Benefit** — regardless of domain, the functional result the consumer wants (cosmetics: `주름개선` · appliances: `발열 적은` · food: `혈행개선`)
  - **Super-concept** — an overarching theme (cosmetics: `안티에이징` · appliances: `고성능` · food: `건강기능식품`)
- **Representative category anchor**: one category-level keyword to serve as the research subject. If an explicit category term exists, use it;
  otherwise, infer the **narrowest category** that the effects/super-concepts commonly belong to.
- **Deficiency-signal preservation principle**: effect keywords are language that directly reveals the consumer's "hidden discomfort/deficiency."
  Do not erase them by abstracting into a category; record the problem situation each effect implies exactly as-is.
- **Allowance for no effect layer**: because the input may be category/brand-centric, there may be no effect keyword at all
  (e.g., `생수, 삼다수, 백산수, 에비앙`). In this case, **leave the deficiency signals blank** and **do not fabricate** effects that are absent.

### Input Variables

| Variable                | Description                                 | Example                                                      |
| ----------------------- | ------------------------------------------- | ------------------------------------------------------------ |
| `{{product_name}}`      | Original input string of **multiple keywords (comma-separated)** | `주름개선 화장품, 주름개선, 안티에이징, 노화방지 화장품, 피부 탄력개선` |
| `{{research_date}}`     | Research reference date (YYYY.MM.DD)         | `2026.07.10`                                                 |
| `{{response_language}}` | Response language                           | `Korean`                                                     |

### Output Format

A markdown document (H1 title + H2 sections). Follow the Phase 1 category format, but add a **Keyword Composition Analysis section at the very front**
and a **Consumer Deficiency Signals section at the very end**.

### Request Model and Parameters

Same as Phase 1.

| Parameter         | Value                                                                       |
| :---------------- | :------------------------------------------------------------------------- | ---- | ---------------------------------------- |
| model             | 'gpt-5.4-nano'                                                             |
| input             | the prompt string built above                                             |
| text              | { format: { type: 'text' }, verbosity: 'low' }                             |
| reasoning         | { effort: 'none', summary: null }                                          |
| tools             | [{ type: 'web_search', user_location: { type: 'approximate', country: 'KR' | 'JP' | 'US' }, search_context_size: 'medium' }] |
| tool_choice       | { type: 'web_search' }                                                     |
| store             | false                                                                      |
| include           | ['web_search_call.action.sources']                                         |
| max_output_tokens | 6000                                                                       |

---

### Prompt Template

```
# Role
Research specialist. The user has provided MULTIPLE comma-separated keywords that describe ONE product/interest area from different angles. Your job is to (a) understand how the keywords relate, (b) research the shared product category with high resolution, and (c) preserve the distinct consumer-deficiency signals the individual keywords reveal — WITHOUT blurring them together.

## INPUT
The input is several comma-separated terms, e.g.:
"주름개선 화장품, 주름개선, 안티에이징, 노화방지 화장품, 피부 탄력개선"

These terms usually MIX information layers. This layering applies to ANY product domain — cosmetics, electronics, food/supplements, appliances, services — NOT just beauty:
- **Category**: a product category (the thing being sold). Cosmetics: 주름개선 화장품 · Electronics: 게이밍 노트북 · Food: 오메가3
- **Effect/Benefit**: a functional result the consumer wants, in ANY domain. Cosmetics: 주름개선 · Electronics: 발열 적은 · Food: 혈행개선
- **Super-concept**: a broader umbrella theme. Cosmetics: 안티에이징 · Electronics: 고성능 · Food: 건강기능식품

Two things you must NOT do:
1. Do NOT merge all terms into one vague subject and search that comma string as-is.
2. Do NOT treat each keyword as a separate unrelated product to research independently.
Treat them as multiple lenses on ONE product category.

# Research Process
1. **Classify** every input keyword as Category / Effect·Benefit / Super-concept / Other.
2. **Anchor**: pick the representative PRODUCT CATEGORY as the research subject.
   - Prefer an explicit category-level keyword.
   - If only effects/super-concepts are given, infer the SMALLEST category that contains them (cosmetics: 주름개선 + 안티에이징 → "안티에이징 화장품"; electronics: 발열 적은 + 고성능 → "게이밍 노트북").
3. **Research** that representative category as a market overview, at the same depth as a single-category study.
4. **Preserve deficiencies**: for each Effect/Benefit keyword, capture the felt problem/situation it implies. These are the strongest CEP clues — keep them explicit and separate; never abstract them away. If the input contains NO effect/benefit keyword (e.g., only categories and brand names like "생수, 삼다수, 에비앙"), leave the deficiency section empty and do NOT invent effects that were not in the input.
5. **Source priority**: Official site > Professional reviews > User reviews > Price comparison > News.
6. **Validate**: prefer sources within 6 months; cross-verify conflicts.

# Output Format
## Document Structure
- **Title**: Single H1 heading (#) — the representative category name, in {{response_language}}. No intro text.
- **Sections**: each with an H2 heading (##), formatted as ## N. <Section title>.

## Required Sections (in order)
## 1. 키워드 구성 분석 (Keyword composition)
- List EVERY input keyword with its layer label: [Category] / [Effect·Benefit] / [Super-concept] / [Other].
- State the chosen representative category and a one-line rationale.

## 2. 카테고리 개요 및 선택 기준
## 3. 추천 제품 (3-5)
## 4. 기능/스펙 비교
## 5. 가격대
## 6. 소비자 결핍 신호 (Consumer deficiency signals)
- One block PER Effect/Benefit keyword.
- For each: the felt discomfort/problem, and the everyday situation in which it surfaces.
- Keep each keyword's signal distinct — do NOT merge them into a single generic paragraph.
- If there are NO Effect/Benefit keywords in the input, write a single line: "효능 키워드 없음" and add nothing else. Do NOT fabricate signals.

## Formatting Rules
- Start with the H1 title immediately. No introductory text.
- Only output the defined sections. No extra sections, disclaimers, or closing remarks.
- Mark uncertain information with [unverified].
- End immediately after the last section.

## Unrecognized Input
If and ONLY if the ENTIRE input is clearly meaningless — random characters, gibberish with no recognizable words (e.g., "dslkfjakldfj8484;;", "aaaaabbbb!!!") — output ONLY this single line and nothing else:
`UNRECOGNIZED_INPUT`
Do NOT return UNRECOGNIZED_INPUT if at least one term is a real word, brand, place, or concept.

# Research Date
- **Research date**: {{research_date}}

# Language
- Write EVERYTHING in **{{response_language}}**.

Input: {{product_name}}
```
