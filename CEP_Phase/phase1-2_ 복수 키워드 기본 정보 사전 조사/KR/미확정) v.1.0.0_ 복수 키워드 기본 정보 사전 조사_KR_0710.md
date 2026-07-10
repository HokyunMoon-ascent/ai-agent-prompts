<!-- v.1.0.0_cep_KR_0710.md (updated 2026-07-10) — Phase 1의 복수 키워드 분기 버전 (미확정 초안) -->

## Phase 1-2 — 복수 키워드 기본 정보 사전 조사 (미확정)

> **분기 조건**: 사용자가 입력한 키워드가 **복수(콤마 구분)** 일 때 Phase 1 대신 이 프롬프트로 진입합니다.
> 단일 키워드면 기존 Phase 1(`기본 정보 사전 조사`)을 그대로 사용합니다.

### Phase 1(단일) 대비 차이점

| 항목      | Phase 1 (단일 키워드)             | Phase 1-2 (복수 키워드 · 본 문서)                                                    |
| --------- | --------------------------------- | ------------------------------------------------------------------------------------ |
| 입력      | 단일 제품/카테고리                | **콤마로 구분된 복수 키워드** (층위 혼합: 카테고리·효능·상위개념)                     |
| 처리 원칙 | 대상 1개를 그대로 조사            | **키워드 층위 분류 → 대표 카테고리 1개로 고해상도 조사 + 효능/결핍 키워드는 별도 보존** |
| 출력 추가 | —                                 | `## 키워드 구성 분석` + `## 소비자 결핍 신호` 섹션 (효능 키워드를 뭉개지 않고 유지)   |
| 유지      | web_search · 소스 우선순위 · 언어 | 전부 동일                                                                            |

### 목적

성격이 다른 복수 키워드가 들어왔을 때, 이를 **하나의 흐릿한 덩어리로 병합하지 않고** 층위별로 분류합니다.
그중 **대표 제품 카테고리 1개**를 골라 고해상도로 기본 정보를 조사하되, 효능/결핍 키워드는
CEP 발굴의 핵심 단서이므로 **별도 신호로 보존**해 다음 단계(Phase 2-2)로 넘깁니다.

### 핵심 개념

- **키워드 층위(Information Layer)** — **모든 도메인(화장품·가전·식품·서비스 등)에 공통 적용**:
  - **카테고리(Category)** — 판매되는 제품군 (화장품: `주름개선 화장품` · 가전: `게이밍 노트북` · 식품: `오메가3`)
  - **효능·속성(Effect/Benefit)** — 도메인 불문, 소비자가 원하는 기능적 결과 (화장품: `주름개선` · 가전: `발열 적은` · 식품: `혈행개선`)
  - **상위개념(Super-concept)** — 포괄 테마 (화장품: `안티에이징` · 가전: `고성능` · 식품: `건강기능식품`)
- **대표 카테고리 앵커**: 조사 subject로 삼을 카테고리 레벨 키워드 1개. 명시적 카테고리어가 있으면 그것을,
  없으면 효능/상위개념이 공통으로 속하는 **가장 좁은 카테고리**를 추론.
- **결핍 신호 보존 원칙**: 효능 키워드는 소비자의 '숨은 불편·결핍'을 직접 드러내는 언어다.
  카테고리로 추상화해 없애지 말고, 각 효능이 함의하는 문제 상황을 그대로 기록한다.
- **효능층 없음 허용 원칙**: 입력이 카테고리·브랜드 위주라 효능 키워드가 하나도 없을 수 있다
  (예: `생수, 삼다수, 백산수, 에비앙`). 이때는 결핍 신호를 **비워 두고**, 없는 효능을 **지어내지 않는다**.

### 입력 변수

| 변수                    | 설명                                        | 예시                                                         |
| ----------------------- | ------------------------------------------- | ------------------------------------------------------------ |
| `{{product_name}}`      | **복수 키워드(콤마 구분)** 원본 입력 문자열 | `주름개선 화장품, 주름개선, 안티에이징, 노화방지 화장품, 피부 탄력개선` |
| `{{research_date}}`     | 조사 기준일 (YYYY.MM.DD)                    | `2026.07.10`                                                 |
| `{{response_language}}` | 응답 언어                                   | `Korean`                                                     |

### 출력 형식

마크다운 문서 (H1 제목 + H2 섹션들). Phase 1 카테고리 포맷을 따르되 **맨 앞에 키워드 구성 분석**,
**맨 뒤에 소비자 결핍 신호** 섹션을 추가합니다.

### 요청 모델 및 파라미터

Phase 1과 동일.

| 파라미터          | 설정값                                                                     |
| :---------------- | :------------------------------------------------------------------------- | ---- | ---------------------------------------- |
| model             | 'gpt-5.4-nano'                                                             |
| input             | 위에서 만든 prompt 문자열                                                  |
| text              | { format: { type: 'text' }, verbosity: 'low' }                             |
| reasoning         | { effort: 'none', summary: null }                                          |
| tools             | [{ type: 'web_search', user_location: { type: 'approximate', country: 'KR' | 'JP' | 'US' }, search_context_size: 'medium' }] |
| tool_choice       | { type: 'web_search' }                                                     |
| store             | false                                                                      |
| include           | ['web_search_call.action.sources']                                         |
| max_output_tokens | 6000                                                                       |

---

### Prompt 템플릿

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
