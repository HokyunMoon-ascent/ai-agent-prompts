<!-- v.1.1.0_cep_KR_0710.md (updated 2026-07-10) — grounding 개정: 입력을 Phase 3.5 증거 유닛으로 교체 -->

## Phase 4 — CEP Trigger Extraction (CEP 상황, 나노인텐트 생성) — v1.1.0 (Grounded)

### v1.0.0 대비 변경점

| 항목      | v1.0.0 (현행)                                        | v1.1.0 (본 문서)                                                             |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| 입력      | Phase 3 산문 마크다운 (`product_research_sections`)  | **Phase 3.5 증거 유닛 JSON** (`evidence_units`)                              |
| Task      | 산문에서 상황 추출 (자유 생성)                        | **증거 유닛 선택·결합으로 장면 조립** (유닛 quote 밖 표현 생성 금지)          |
| 근거 연결 | `evidence`(문장) + `section_refs`(섹션 번호)          | `evidence_unit_ids` + `source_ref` + **RTB(verbatim 재인용)**                |
| 출력      | `{situation, nanoIntents×3, evidence, section_refs}` | `{situation, w7, nanoIntents×3, kbf_hints, rtb, source_ref, evidence_unit_ids}` |
| 유지      | —                                                    | 다양성 제약 · 7W soft quota · 수량 제약(정확히 N개) · JSON-only 규칙 전부 유지 |

### 목적

Phase 3.5의 증거 유닛에서 **구체적인 CEP 상황과 나노인텐트를 조립**합니다.
v1.0.0과 달리 자유 생성이 아니라 **유닛 선택·결합**이므로, 카드의 모든 구체어(시간·장소·발화·행동)가 인용으로 역추적됩니다.

### 핵심 개념

- **CEP / 나노인텐트 / 7W's Framework**: v1.0.0과 동일
- **조립(assembly)**: 같은 `source_ref` 안의 유닛 1~3개를 결합해 하나의 장면 문장을 만드는 것. 서로 다른 섹션의 유닛 결합은 금지(맥락 합성 방지)
- **구체어 대응 원칙**: 카드에 등장하는 모든 시간·장소·발화·행동 표현은 결합한 유닛의 `quote` 안에 실재해야 함
- **4대 환각 유형 금지** (Phase 3.5 문서와 동일 정의): ① 시간 단정(Time assertion) ② 없는 장소(Invented place) ③ 가짜 인용(Fake quotation) ④ 없는 행동(Invented action)

### 입력 변수

| 변수                      | 설명                                     | 예시               |
| ------------------------- | ---------------------------------------- | ------------------ |
| `{{product_name}}`        | 제품명                                   | `버티컬 마우스`    |
| `{{country}}`             | 국가 코드                                | `kr`               |
| `{{evidence_units}}`      | **Phase 3.5 출력 JSON (증거 유닛 배열)** | (JSON)             |
| `{{requested_count}}`     | 추출할 CEP 상황 개수                     | `10`               |
| `{{category}}`            | [선택] 제품 카테고리                     | `마우스, 입력장치` |
| `{{existing_situations}}` | [선택] 기존 CEP 목록 (중복 방지용)       | (이미 생성된 목록) |

### 출력 형식

JSON 배열 (정확히 `{{requested_count}}`개)

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

### 요청 모델 및 파라미터

v1.0.0과 동일 (gpt-5.4-nano · tools [] · reasoning none · max_output_tokens 32768).

---

### Prompt 템플릿

````
# Role
You are a consumer behavior analyst specializing in Category Entry Point (CEP) identification.
You do NOT invent situations. You **assemble** CEP situations from pre-verified evidence units, so that every concrete detail in your output traces back to a verbatim quote.

{{category_section}}
# Evidence Units (pre-verified, verbatim-grounded)
Each unit contains a verbatim quote from consumer research, plus the w7 fields, nano intent candidates, and KBF hints that the quote directly supports.

{{evidence_units}}

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
