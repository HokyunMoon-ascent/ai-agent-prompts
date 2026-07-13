<!-- v.1.0.0_cep_EN_0710.md (updated 2026-07-10) -->

## Phase 3.5 — Evidence Decomposition

### Purpose

This phase **decomposes verbatim quotes into structured "evidence units"** from the web-search prose produced in Phase 3 (CEP Insight Research) and Phase 3-1 (Latent Intent).
It is a grounding phase that **prevents downstream stages** (Phase 4 CEP assembly, Phase 5 KBF finalization) **from inventing any expression outside these units**, thereby blocking source-absent elaboration (hallucination) at generation time.

> Background: In the Hallucination_test validation (e.g., project 1328), the cause of every fail/warning card was
> **source-absent elaboration** introduced during the "prose research → straight to card" process.
> Moving what used to be done in post-hoc repropose — "decompose verified quotes alone into w7 / nano_intent / KBF / RTB + explicit source_ref" —
> into a formal pipeline stage is what this Phase does.

### Core Concepts

- **Evidence Unit**: A minimal evidence unit that, centered on one quote extracted verbatim from the body text, bundles the w7 sub-fields, nano-intent candidates, KBF hints, RTB, and source_ref that the quote directly supports
- **verbatim**: A word-for-word copy of a sentence from the body text. Paraphrasing, summarizing, and synthesis are forbidden
- **RTB (Reason To Believe)**: A quote refined into a re-citable form for consumers/readers (trimming only, no added content)
- **source_ref**: The body-text section number the quote belongs to (`§N` notation)
- **The 4 hallucination types** (patterns repeatedly confirmed in validation — explicitly forbidden in this phase):
  1. **Time assertion** — specifying a period/timing not in the source (e.g., "첫 주", "주말")
  2. **Invented place** — adding a location not in the source (e.g., "도서관")
  3. **Fake quotation** — wrapping a paraphrase in quotation marks as if it were an actual utterance
  4. **Invented action** — describing an action not in the source (e.g., "다시 검색")

### Input Variables

| Variable                        | Description                                              | Example           |
| ------------------------------- | -------------------------------------------------------- | ----------------- |
| `{{product_name}}`              | Product name                                             | `버티컬 마우스`   |
| `{{response_language}}`         | Response language                                        | `Korean`          |
| `{{product_research_sections}}` | Phase 3 result (markdown including section numbers)      | (markdown text)   |
| `{{latent_intent_sections}}`    | [Optional] Phase 3-1 result — decompose alongside if present | (markdown text) |
| `{{web_sources}}`               | [Optional] Phase 3 `web_search_call.action.sources` list | (url + title)     |

### Output Format

JSON array (list of evidence units — no count limit, as many as the body text supports)

```json
[
  {
    "unit_id": 1,
    "source_ref": "§1",
    "quote": "소파나 침대 옆에 노트북을 두고(팔이 자연스럽게 내려가지 않는 자세로) 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요.",
    "url": "https://www.hankyung.com/article/2021010599531",
    "url_title": "버티컬 마우스로 손목 건강 챙긴다 | 한국경제",
    "w7": {
      "where": "소파·침대 옆(팔이 자연스럽게 내려가지 않는 자세)",
      "while_": "손목이 몸쪽으로 꺾이는 느낌이 반복됨"
    },
    "nano_intent_candidates": [
      "노트북 중심 자세에서 손목이 꺾이지 않는 그립으로 바꾸기"
    ],
    "kbf_hints": [
      "손목을 덜 꺾이게 하는 수직(핸드셰이크) 그립 각도"
    ],
    "rtb": "\"소파나 침대 옆에 노트북을 두고 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요\" (hankyung)"
  }
]
```

- `w7` keys: `why` / `when` / `where` / `while_` / `with_whom` / `with_what` / `how_feeling` — **include only the fields the quote directly supports** (omit the rest; do not fill with null)
- `nano_intent_candidates`: 0–3 items. Purposes expressible using only the quote's content
- `kbf_hints`: 0–3 items. Only concrete attributes mentioned/implied in the quote (numeric values only if present in the quote)
- `url`/`url_title`: The body-text citation link, or a match from `{{web_sources}}`. Empty string if unknown

### Request Model and Parameters

| Parameter         | Value                                                               | Note                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | Fixed                              |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | Single user message + input_text part |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  | No web search — decomposition only |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt Template

````
# Role
You are an evidence curation specialist. Your job is to decompose consumer research text into **verbatim evidence units** — structured records where every field is directly supported by an exact quote from the source text. You NEVER add information that is not in the source.

# Source Text (Product Research)
The following is web-researched consumer context for **"{{product_name}}"**. Section headings are numbered (## N. ...); use N as the section reference.

{{product_research_sections}}

{{latent_intent_block}}

# Web Sources (for URL matching)
{{web_sources_block}}

# Task
Decompose the source text into evidence units. Work section by section, paragraph by paragraph:

1. For each paragraph that describes a distinct consumer context, extract ONE evidence unit.
2. `quote` = the sentence(s) copied **verbatim** from that paragraph. Copy exactly — do NOT paraphrase, summarize, merge sentences from different paragraphs, or "clean up" wording.
3. `source_ref` = "§N" where N is the section number the quote belongs to.
4. `url` / `url_title` = the citation link attached to that paragraph in the source text (or the best match from Web Sources). Use "" if unknown. Never invent a URL.
5. `w7` = only the dimensions the quote itself supports, phrased using the quote's own words as much as possible:
   - why (motivation) / when (time) / where (place) / while_ (parallel activity) / with_whom / with_what / how_feeling
   - **Omit any key the quote does not support. Sparse units are correct; padded units are wrong.**
6. `nano_intent_candidates` = 0–3 consumer purposes that can be stated using ONLY what the quote says. Do not repeat the product/category name. No generic phrases ("need it", "ran out").
7. `kbf_hints` = 0–3 concrete product attributes mentioned or directly implied by the quote (form, structure, material, feature). Include a numeric value ONLY if it appears in the quote verbatim.
8. `rtb` = the quote trimmed into a re-citable form, wrapped in quotation marks, with a short source tag. Trimming only — never add or alter words.

# Hard Grounding Rules (violations invalidate the unit)
- The quote must exist verbatim in the source text.
- Every w7 value, nano intent candidate, and KBF hint must be traceable to the quote it belongs to — not to your general knowledge of the category.
- The following four hallucination patterns are strictly FORBIDDEN anywhere in the output:
  1. **Time assertion**: adding a period/timing the source does not state (e.g., "첫 주", "주말").
  2. **Invented place**: adding a location the source does not mention (e.g., "도서관").
  3. **Fake quotation**: wrapping a paraphrase in quotation marks as if it were spoken/written in the source.
  4. **Invented action**: describing an action the source does not describe (e.g., "다시 검색").
- If a paragraph is too vague to support any w7 field, output the unit with quote + source_ref only (empty w7 object is allowed).

# Language
Write all field values in **{{response_language}}**, except `quote`/`rtb` which must preserve the source language verbatim.

# OUTPUT RULES (STRICT, JSON-ONLY)
- Return ONLY a single valid JSON array.
- Do NOT wrap the JSON in Markdown code fences (no ```).
- Do NOT add any prose, explanation, or headings outside JSON.
- Do NOT add trailing commas. Use double quotes for ALL keys and string values.
- `unit_id` must be a number, 1-based, sequential.
````

#### Template Assembly Rules

| Block                     | Value                                                                                                |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| `{{latent_intent_block}}` | If `latent_intent_sections` exists, `# Source Text (Latent Intent)\n{{latent_intent_sections}}`; otherwise empty string |
| `{{web_sources_block}}`   | If `web_sources` exists, a url·title list; otherwise `(none provided)`                                |

---

### Appendix — Dry-run Example (project 1328 §1 real data)

The actual paragraph from Phase 3 output §1:

> 재택근무를 처음 시작했을 때(혼자 컴퓨터 앞에서 작업하는 시간이 길어질 때) 목·어깨 불편을 먼저 느끼고, 그다음엔 노트북 환경에서 "마우스까지 인체공학적으로 못 맞추는" 문제가 크게 와 닿아요. ([logitech.com](https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf))

The evidence unit this paragraph produces:

```json
{
  "unit_id": 2,
  "source_ref": "§1",
  "quote": "재택근무를 처음 시작했을 때(혼자 컴퓨터 앞에서 작업하는 시간이 길어질 때) 목·어깨 불편을 먼저 느끼고, 그다음엔 노트북 환경에서 \"마우스까지 인체공학적으로 못 맞추는\" 문제가 크게 와 닿아요.",
  "url": "https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf",
  "url_title": "",
  "w7": {
    "why": "재택 전환으로 노트북 중심 작업이 길어짐",
    "while_": "목·어깨 불편을 먼저 느낌",
    "how_feeling": "'마우스까지 인체공학적으로 못 맞춘다'는 답답함"
  },
  "nano_intent_candidates": [
    "노트북 중심 자세에서 목·어깨-손목 부담을 함께 줄이기"
  ],
  "kbf_hints": [],
  "rtb": "\"노트북 환경에서 '마우스까지 인체공학적으로 못 맞추는' 문제가 크게 와 닿아요\" (logitech)"
}
```

Points to note:
- There is **no** period like "첫 주" in `when` — because the source only says "처음 시작했을 때" (the AS-IS Phase 4 invented "첫 주" here and received a warning)
- The quoted excerpt in `how_feeling` is verbatim from the source — the AS-IS fake quotation ("손목도 마우스로 더 안 맞는 것 같다") is blocked at the source
- `kbf_hints` is an empty array — this quote has no mention of product attributes, so it is not padded (the vertical-grip hint comes from the hankyung quote unit in §1)

The target behavior of this phase is that with just these two units (§1 logitech + hankyung), the result Phase 4 assembles becomes identical to the **CEP 4 revised proposal** in `Hallucination_test/repropose/1328.json`.
