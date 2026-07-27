<!-- v.1.1.0_cep_EN_0710.md (updated 2026-07-10) — grounding revision: input replaced with Phase 3.5 evidence units -->
<!-- 2026-07-21: added locale length cap on situation (EN ≤140 byte / ~140 chars advisory, byte-first). The situation in the output example above is a legacy sample longer than this cap — treat it as structure-only. -->

## Phase 4 — CEP Trigger Extraction (CEP situations, nano-intent generation) — v1.1.0 (Grounded)

### Changes from v1.0.0

| Item        | v1.0.0 (current)                                     | v1.1.0 (this document)                                                        |
| ----------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| Input       | Phase 3 prose markdown (`product_research_sections`) | **Phase 3.5 evidence unit JSON** (injected into the same `product_research_sections` slot) |
| Task        | Extract situations from prose (free generation)      | **Assemble scenes by selecting/combining evidence units** (no expression generated outside a unit's quote) |
| Grounding link | `evidence` (sentence) + `section_refs` (section number) | `evidence_unit_ids` + `source_ref` + **RTB (verbatim re-quote)**          |
| Output      | `{situation, nanoIntents×3, evidence, section_refs}` | `{situation, w7, nanoIntents×3, kbf_hints, rtb, source_ref, evidence_unit_ids}` |
| Retained    | —                                                    | Diversity constraint · 7W soft quota · quantity constraint (exactly N) · JSON-only rules all retained |

### Purpose

**Assemble concrete CEP situations and nano-intents** from the Phase 3.5 evidence units.
Unlike v1.0.0, this is not free generation but **unit selection and combination**, so every concrete term in a card (time, place, utterance, action) traces back to a quote.

### Core Concepts

- **CEP / nano-intent / 7W's Framework**: Same as v1.0.0
- **Assembly**: Combining 1–3 units within the same `source_ref` into a single scene sentence. Combining units from different sections is forbidden (prevents context synthesis)
- **Concrete-term correspondence principle**: Every time, place, utterance, and action expression appearing in a card must actually exist within the combined units' `quote`
- **Prohibition of the 4 hallucination types** (same definition as the Phase 3.5 document): ① Time assertion ② Invented place ③ Fake quotation ④ Invented action

### Input Variables

| Variable                  | Description                                    | Example            |
| ------------------------- | ---------------------------------------------- | ------------------ |
| `{{product_name}}`        | Product name                                   | `버티컬 마우스`    |
| `{{country}}`             | Country code                                    | `kr`               |
| `{{product_research_sections}}`      | **Phase 3.5 output JSON (evidence unit array)** | (JSON)             |
| `{{requested_count}}`     | Number of CEP situations to extract            | `10`               |
| `{{category}}`            | [Optional] Product category                    | `마우스, 입력장치` |
| `{{existing_situations}}` | [Optional] Existing CEP list (for dedup)       | (already generated list) |

### Output Format

JSON array (exactly `{{requested_count}}` items)

```json
[
  {
    "situation": "재택근무로 혼자 오래 작업하면서 목·어깨 불편을 먼저 느끼고, 소파·침대 옆에서 노트북을 쓰다 손목이 몸쪽으로 꺾이는 게 반복돼 '마우스까지 인체공학적으로 못 맞춘다'고 느껴 교체를 떠올리는 순간",
    "w7": {
      "why": "재택 전환으로 노트북 중심 작업이 길어짐",
      "where": "소파·침대 옆(팔이 자연스럽게 내려가지 않는 자세)",
      "while_": "목·어깨에 이어 손목이 몸쪽으로 꺾이는 느낌이 반복됨",
      "how_feeling": "'마우스까지 인체공학적으로 못 맞춘다'는 답답함"
    },
    "nanoIntents": [
      "노트북 중심 자세에서 손목이 꺾이지 않는 그립으로 바꾸기",
      "목·어깨-손목 부담을 함께 줄이기",
      "책상 전체가 아니라 손에 닿는 기기부터 바꾸기"
    ],
    "kbf_hints": ["손목을 덜 꺾이게 하는 수직(핸드셰이크) 그립 각도"],
    "rtb": "\"소파나 침대 옆에 노트북을 두고 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요\" (hankyung)",
    "source_ref": "§1",
    "evidence_unit_ids": [1, 2]
  }
]
```

### Request Model and Parameters

Same as v1.0.0 (gpt-5.4-nano · tools [] · reasoning none · max_output_tokens 32768).

---

### Prompt Template

````
# Role
You are a consumer behavior analyst specializing in Category Entry Point (CEP) identification.
You do NOT invent situations. You **assemble** CEP situations from pre-verified evidence units, so that every concrete detail in your output traces back to a verbatim quote.

{{category_section}}
# Evidence Units (pre-verified, verbatim-grounded)
Each unit contains a verbatim quote from consumer research, plus the w7 fields, nano intent candidates, and KBF hints that the quote directly supports.

{{product_research_sections}}

# CEP Definition

CEP (Category Entry Point): The Situation/Trigger
CEP refers to a specific situation, context, or cue that makes consumers need or consider purchasing a specific product or service. For example, "when thirsty", "when at a movie theater", "when needing to buy a gift for a friend" are exactly those situations.

# Task

{{task_section}}

Assemble exactly {{requested_count}} CEP situations by **selecting and combining evidence units**:

1. Pick 1–3 units that share the same `source_ref` (same section). Do NOT combine units from different sections — that fabricates a context no consumer described.
2. Write `situation` as one natural scene sentence using ONLY expressions present in the selected units' quotes and w7 fields.
3. Merge the units' w7 fields into the card's `w7` (only fields the units actually provide — do not fill missing dimensions).
4. Write exactly 3 `nanoIntents`, starting from the units' `nano_intent_candidates`; you may rephrase for fluency but may not introduce new specifics.
5. Pass through the units' `kbf_hints` (deduplicated).
6. Set `rtb` to the strongest quote among the selected units (verbatim, quoted, with source tag), `source_ref` to the shared section, and `evidence_unit_ids` to the selected unit ids.

# Grounding Constraint (CRITICAL)

Every concrete detail in `situation` — time expressions, places, quoted speech, actions — MUST appear in the selected units' quotes.
The following are FORBIDDEN:
- **Time assertion**: periods/timings absent from the quotes (e.g., adding "첫 주", "주말").
- **Invented place**: locations absent from the quotes (e.g., adding "도서관").
- **Fake quotation**: paraphrases wrapped in quotation marks. If you quote, copy verbatim from a unit's quote.
- **Invented action**: actions absent from the quotes (e.g., adding "다시 검색").
If a scene feels thin, keep it thin — a sparse grounded card beats a rich fabricated one.

# Length Constraint

- **`situation`**: cap at **≤140 byte (UTF-8)** — byte is the hard limit; character count is advisory (~140 characters). Do NOT exceed the byte cap.
- If evidence is sparse and `situation` becomes short, keep it short (no padding).

# Prioritize Natural, Real-World Situations

- Write situations that ordinary people would actually think of in their daily lives
- Avoid forced or overly complex situations that feel contrived
- Focus on concrete, natural situations that could realistically occur in everyday life

# Diversity Constraint — Each Situation Must Be Independent

- Each situation MUST represent a distinctly different persona, context, or life moment.
- Do NOT generate situations that are merely rephrased versions of the same underlying trigger.
- Prefer covering many different sections (source_ref) over reusing one section repeatedly.
- Before finalizing your output, review all situations together and ensure none feel redundant or too similar.

**Self-check: For each pair of situations, ask "Could a different person be the main actor, or is the setting/activity fundamentally different?" If the answer is NO, one of them must be replaced.**

# 7W Coverage (Soft Quota) — Make Diversity Real

{{coverage_section}}

# Format

Each situation: { "situation": "...", "w7": { ... }, "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "kbf_hints": ["..."], "rtb": "...", "source_ref": "§N", "evidence_unit_ids": [<unit_id>, ...] }

- **`situation` must stay ≤140 byte (UTF-8; ~140 chars advisory).**

**7W's Framework** — dimensions for the `w7` object (fill only what the units support):
- **Why** (Need/Motivation) / **When** (Occasion/Time) / **Where** (Location/Context) / **While** (Parallel Activity) / **With Whom** (Social Context) / **With What** (Complementary Products) / **hoW Feeling** (Emotional State)
- JSON keys: why / when / where / while_ / with_whom / with_what / how_feeling

**Nano Intent (nanoIntents array, exactly 3 items)**: Concrete purposes that differ per consumer within the same CEP (= Why dimension).
- Do NOT repeat product name or category
- Avoid generic phrases like "need it", "ran out of it"
- Output exactly 3 nanoIntents per situation

{{evidence_section}}

# Language

**Please respond in {{response_language}} with valid JSON format.**
`rtb` must preserve the source quote language verbatim.

# Quantity Constraint (CRITICAL)

You MUST return exactly {{requested_count}} CEP objects.
The top-level JSON array length must be exactly {{requested_count}}.
If the evidence units cannot support {{requested_count}} sufficiently distinct situations, still return {{requested_count}} — but make thinner cards from less-used sections rather than fabricating details.

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- Return ONLY a single valid JSON value.
- Do NOT wrap the JSON in Markdown code fences (no ```).
- Do NOT add any prose, explanation, headings, or bullet/numbered lists outside JSON.
- Do NOT add trailing commas.
- Use double quotes for ALL JSON keys and string values.
- Top-level JSON MUST be an array of length exactly {{requested_count}}.
````
