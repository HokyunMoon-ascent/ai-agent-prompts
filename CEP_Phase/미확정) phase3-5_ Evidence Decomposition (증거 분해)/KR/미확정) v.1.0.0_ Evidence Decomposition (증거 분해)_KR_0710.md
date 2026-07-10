<!-- v.1.0.0_cep_KR_0710.md (updated 2026-07-10) -->

## Phase 3.5 — Evidence Decomposition (증거 분해)

### 목적

Phase 3(CEP Insight Research)·Phase 3-1(Latent Intent)의 웹서치 산문에서 **verbatim 인용을 구조화된 '증거 유닛(evidence unit)'으로 분해**합니다.
이후 단계(Phase 4 CEP 조립, Phase 5 KBF 확정)는 이 유닛 **밖의 표현을 새로 만들 수 없게** 하여, 소스에 없는 구체화(환각)를 생성 단계에서 차단하는 grounding 단계입니다.

> 배경: Hallucination_test 검증(예: project 1328)에서 fail/warning 카드의 원인은 모두
> "산문 리서치 → 카드 직행" 과정에서 생긴 **소스에 없는 구체화**였음.
> 사후 repropose에서 수행하던 "검증 인용만으로 w7·nano_intent·KBF·RTB 분해 + source_ref 명시"를
> 파이프라인의 정식 단계로 이동한 것이 본 Phase.

### 핵심 개념

- **증거 유닛 (Evidence Unit)**: 본문에서 verbatim으로 발췌한 quote 1개를 중심으로, 그 quote가 직접 뒷받침하는 w7 부분필드·나노인텐트 후보·KBF 힌트·RTB·source_ref를 묶은 최소 근거 단위
- **verbatim**: 본문 문장을 글자 그대로 복사한 것. 의역·요약·합성 금지
- **RTB (Reason To Believe)**: 소비자/독자에게 재인용 가능한 형태로 다듬은 quote (내용 추가 없이 트리밍만 허용)
- **source_ref**: quote가 속한 본문 섹션 번호 (`§N` 표기)
- **4대 환각 유형** (검증에서 반복 확인된 패턴 — 본 단계에서 명시적 금지):
  1. **시간 단정** — 소스에 없는 기간/시점 특정 (예: "첫 주", "주말")
  2. **없는 장소** — 소스에 없는 장소 추가 (예: "도서관")
  3. **가짜 인용** — 의역을 따옴표로 감싸 실재 발화처럼 표기
  4. **없는 행동** — 소스에 없는 행동 서술 (예: "다시 검색")

### 입력 변수

| 변수                            | 설명                                                     | 예시              |
| ------------------------------- | -------------------------------------------------------- | ----------------- |
| `{{product_name}}`              | 제품명                                                   | `버티컬 마우스`   |
| `{{response_language}}`         | 응답 언어                                                | `Korean`          |
| `{{product_research_sections}}` | Phase 3 결과 (섹션 번호 포함 마크다운)                   | (마크다운 텍스트) |
| `{{latent_intent_sections}}`    | [선택] Phase 3-1 결과 — 있으면 함께 분해                 | (마크다운 텍스트) |
| `{{web_sources}}`               | [선택] Phase 3 `web_search_call.action.sources` 목록     | (url + title)     |

### 출력 형식

JSON 배열 (증거 유닛 목록 — 개수 제한 없음, 본문이 뒷받침하는 만큼)

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

- `w7` 키: `why` / `when` / `where` / `while_` / `with_whom` / `with_what` / `how_feeling` — **quote가 직접 뒷받침하는 필드만 포함** (나머지 키는 생략, null 채우기 금지)
- `nano_intent_candidates`: 0~3개. quote의 내용만으로 표현 가능한 목적
- `kbf_hints`: 0~3개. quote에 언급/함의된 구체 속성만 (수치는 quote에 있을 때만)
- `url`/`url_title`: 본문 인용 링크 또는 `{{web_sources}}`에서 매칭. 불명이면 빈 문자열

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                              | 비고                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | 고정                               |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | 단일 user 메시지 + input_text 파트 |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  | 웹서치 없음 — 분해 전용            |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt 템플릿

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

#### 템플릿 조립 규칙

| 블록                      | 값                                                                                                   |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| `{{latent_intent_block}}` | `latent_intent_sections`가 있으면 `# Source Text (Latent Intent)\n{{latent_intent_sections}}`, 없으면 빈 문자열 |
| `{{web_sources_block}}`   | `web_sources`가 있으면 url·title 목록, 없으면 `(none provided)`                                       |

---

### 부록 — Dry-run 예시 (project 1328 §1 실데이터)

Phase 3 산출물 §1의 실제 문단:

> 재택근무를 처음 시작했을 때(혼자 컴퓨터 앞에서 작업하는 시간이 길어질 때) 목·어깨 불편을 먼저 느끼고, 그다음엔 노트북 환경에서 "마우스까지 인체공학적으로 못 맞추는" 문제가 크게 와 닿아요. ([logitech.com](https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf))

이 문단이 만드는 증거 유닛:

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

주목할 점:
- `when`에 "첫 주" 같은 기간이 **없음** — 소스가 "처음 시작했을 때"까지만 말하므로 (AS-IS Phase 4는 여기서 "첫 주"를 만들어 warning을 받았음)
- `how_feeling`의 따옴표 인용이 소스 verbatim — AS-IS의 가짜 인용("손목도 마우스로 더 안 맞는 것 같다")이 원천 차단됨
- `kbf_hints`가 빈 배열 — 이 quote에는 제품 속성 언급이 없으므로 억지로 채우지 않음 (수직 그립 힌트는 §1의 hankyung quote 유닛에서 나옴)

이 유닛 2개(§1 logitech + hankyung)만으로 Phase 4가 조립한 결과가 `Hallucination_test/repropose/1328.json`의 **CEP 4 수정안**과 동일해지는 것이 본 단계의 목표 동작입니다.
