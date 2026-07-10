<!-- v.1.0.0_cep_KR_0710.md (updated 2026-07-10) — Phase 2의 복수 키워드 분기 버전 (미확정 초안) -->

## Phase 2-2 — 복수 키워드 Product Anchors (카테고리 앵커 + CEP 시드 추출) (미확정)

> **분기 조건**: 사용자가 입력한 키워드가 **복수(콤마 구분)** 일 때 Phase 2 대신 이 프롬프트로 진입합니다.
> 단일 키워드면 기존 Phase 2(`Product Anchors`)를 그대로 사용합니다.
> 입력은 Phase 1-2(복수 키워드 기본 정보 사전 조사)의 산출물을 받습니다.

### Phase 2(단일) 대비 차이점

| 항목      | Phase 2 (단일 키워드)              | Phase 2-2 (복수 키워드 · 본 문서)                                                          |
| --------- | ---------------------------------- | ------------------------------------------------------------------------------------------ |
| 입력      | 단일 제품명 + 기본 조사 요약       | **복수 키워드** + Phase 1-2 요약                                                           |
| category  | 제품 전체를 대표하는 명사 2-4개    | 동일. 단, **카테고리 층위 키워드에서만** 도출                                              |
| 출력 추가 | `{ category }`                     | `{ category, cep_seeds }` — **효능/상위개념 키워드를 버리지 않고 `cep_seeds`로 보존**       |
| 핵심 규칙 | "feature/perspective는 category 아님" | 동일 규칙 유지 + **효능어는 삭제·추상화 금지, `cep_seeds`로 방출**해 Phase 3·4가 소비       |

### 목적

복수 키워드에서 **벡터 검색용 카테고리 앵커**를 추출합니다.
단, 효능·속성·상위개념 키워드는 카테고리로 뭉개 버리지 않고 **`cep_seeds`(CEP·나노인텐트 시드)로 분리 보존**합니다.
효능 키워드(예: `주름개선`, `피부 탄력개선`)는 소비자 결핍을 드러내는 CEP 발굴의 핵심 단서이기 때문입니다.

### 핵심 개념

- **category**: 이후 벡터 검색에 쓰이는 제품 카테고리 앵커. **카테고리 층위 키워드에서만** 도출하며,
  효능·속성·상위개념·브랜드/모델명은 포함하지 않는다.
- **cep_seeds**: category에 넣지 않은 효능/속성/상위개념 키워드를 **원문 그대로 보존**한 목록.
  Phase 3(CEP Insight)·Phase 4(CEP Trigger·나노인텐트)에서 결핍/상황 탐색의 seed로 사용된다.
  효능은 화장품만의 요소가 아니라 **모든 도메인(가전·식품·서비스 등)**에서 나타난다
  (가전: `발열 적은` · 식품: `혈행개선` · 서비스: `재고관리`).
- **입도 분리 원칙**: "효능은 category가 아니다"는 배제 규칙과, "효능은 버리지 않는다"는 보존 규칙을
  동시에 만족시키기 위해 category와 cep_seeds **두 필드로 분리**한다.
- **cep_seeds 없음 허용 원칙**: 입력이 카테고리·브랜드 위주라 효능/상위개념이 하나도 없으면
  `cep_seeds`는 **빈 배열 `[]`**로 두고, 입력에 없던 키워드를 **지어내지 않는다**.

### 입력 변수

| 변수                         | 설명                                | 예시                                                         |
| ---------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| `{{product_name}}`           | **복수 키워드(콤마 구분)** 원본 입력 | `주름개선 화장품, 주름개선, 안티에이징, 노화방지 화장품, 피부 탄력개선` |
| `{{country}}`                | 국가 코드                           | `kr`                                                         |
| `{{response_language}}`      | 출력 언어 (ko/en/ja)                | `ko`                                                         |
| `{{basic_research_summary}}` | Phase 1-2 결과 요약                 | (마크다운 텍스트)                                            |

### 출력 형식

JSON 객체

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

- `type` 허용값: `effect`(효능·속성) / `super_concept`(상위개념).
- 카테고리 층위 키워드(예: `주름개선 화장품`, `노화방지 화장품`)는 `cep_seeds`에 넣지 않고 `category` 도출에만 사용.

### 요청 모델 및 파라미터

Phase 2와 동일.

| 파라미터  | 설정값                                                                                        |
| :-------- | :-------------------------------------------------------------------------------------------- |
| model     | 'gpt-5.4-nano'                                                                                |
| input     | buildMultiAnchorsPrompt({ productName, country, responseLanguage, basicResearchSummary })     |
| text      | { format: { type: 'text' } }                                                                  |
| reasoning | { effort: 'none' }                                                                            |

---

### Prompt 템플릿

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
