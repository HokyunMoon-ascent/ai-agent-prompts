<!-- v.1.0.1_phase4_CEP 상황 생성_KR_0831.md (updated 2026-08-31) -->
<!-- 베이스: v.1.0.0_ CEP Trigger Extraction (CEP 상황, 나노인텐트 생성)_KR_0329.md
     이번 개정(v.1.0.1)에서 바꾼 것은 나노인텐트 생성 제거 하나뿐입니다. 그 외 문구는 v.1.0.0 그대로입니다.
     ⚠ 백엔드 정합 필요: (1) 출력 스키마에서 nanoIntents 파싱 제거
     (2) Phase 5·5-1로 넘기는 cep_situations 조립 시 nanoIntents 주입 제거
     (3) task_section·evidence_section 주입 문구에 나노인텐트 언급이 남아 있지 않은지 확인 -->

## Phase 4 — CEP Trigger Extraction (CEP 상황 생성)

### 목적

Product Research 결과에서 **구체적인 CEP(Category Entry Point) 상황**을 추출합니다.  
모든 상황은 리서치 본문에 실재하는 근거로 뒷받침되어야 하며, 근거 없는 디테일 생성을 금지합니다.

> 나노인텐트(Nano Intent) 생성은 Web Search 근거에 기반하지 않는 결과물이므로 제거되었습니다.
> v.1.0.0 문서는 머리말에서 제거를 선언해 놓고 템플릿에는 `nanoIntents` 출력이 그대로 남아 있었습니다. v.1.0.1은 그 모순을 없앤 판본입니다.

### 개정 이력

| 버전    | 날짜  | 변경                                                                                             |
| ------- | ----- | ------------------------------------------------------------------------------------------------ |
| v.1.0.1 | 08-31 | 템플릿에서 `nanoIntents` 출력 필드·Nano Intent 생성 지시·Why→nanoIntents 매핑 삭제. 그 외 무변경 |
| v.1.0.0 | 03-29 | 최초 판본                                                                                        |

### 핵심 개념

- **CEP (Category Entry Point)**: 소비자가 특정 제품 카테고리를 필요로 하거나 떠올리게 되는 구체적 상황/트리거
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why — 다양성 점검용 관찰 렌즈 (강제 충족 쿼터 아님)
- **구체어 대응 원칙**: 상황에 등장하는 모든 시간·장소·발화·행동 표현은 제품 리서치 본문에 실재해야 함
- **4대 환각 유형 금지**: ① 시간 단정(Time assertion) ② 없는 장소(Invented place) ③ 가짜 인용(Fake quotation) ④ 없는 행동(Invented action)

### 입력 변수

v.1.0.0과 동일합니다. **신규 변수 0개 · 삭제 변수 0개** — 백엔드 주입 코드를 고치지 않아도 됩니다.

| 변수                            | 설명                               | 예시               |
| ------------------------------- | ---------------------------------- | ------------------ |
| `{{product_name}}`              | 제품명                             | `탈모 샴푸`        |
| `{{country}}`                   | 국가 코드                          | `kr`               |
| `{{product_research_sections}}` | CEP Insight Research 결과 섹션들   | (마크다운 텍스트)  |
| `{{requested_count}}`           | 추출할 CEP 상황 개수               | `10`               |
| `{{category}}`                  | [선택] 제품 카테고리               | `샴푸, 헤어케어`   |
| `{{existing_situations}}`       | [선택] 기존 CEP 목록 (중복 방지용) | (이미 생성된 목록) |

### 출력 형식

JSON 배열 (정확히 `{{requested_count}}`개)

```json
[
  {
    "situation": "아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때",
    "evidence": "샤워하고 나면 배수구에 머리카락이 한 움큼씩 빠져 있어요",
    "section_refs": [2]
  }
]
```

`nanoIntents` 키는 반환하지 않습니다.

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                              | 비고                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | 고정                               |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | 단일 user 메시지 + input_text 파트 |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  |                                    |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt 템플릿

````
<!-- v.1.0.1_phase4_CEP 상황 생성_KR_0831.md (updated 2026-08-31) -->
# Role
You are a consumer behavior analyst specializing in Category Entry Point (CEP) identification.
Your expertise is in uncovering the real-life situations, triggers, and contexts that lead consumers to think of or need a specific product category.

{{category_section}}
# Product Research Results
{{product_research_sections}}

# CEP Definition

CEP (Category Entry Point): The Situation/Trigger
CEP refers to a specific situation, context, or cue that makes consumers need or consider purchasing a specific product or service. For example, "when thirsty", "when at a movie theater", "when needing to buy a gift for a friend" are exactly those situations.

# Task

{{task_section}}

# Prioritize Natural, Real-World Situations

- **Write situations that ordinary people would actually think of in their daily lives**
- Avoid forced or overly complex situations that feel contrived
- Focus on concrete, natural situations that could realistically occur in everyday life
- Think about what real consumers would actually search for or think about

# Diversity Constraint — Each Situation Must Be Independent

- **Each situation MUST represent a distinctly different persona, context, or life moment.**
- Do NOT generate situations that are merely rephrased versions of the same underlying trigger.
- If two situations share the same core need, emotional state, or context, keep only the most specific one and replace the other with a genuinely new situation.
- Before finalizing your output, review all situations together and ensure none feel redundant or too similar.

**Self-check: For each pair of situations, ask "Could a different person be the main actor, or is the setting/activity fundamentally different?" If the answer is NO, one of them must be replaced.**

# 7W Coverage (Soft Quota) — Make Diversity Real

{{coverage_section}}

# Format

Each situation: { "situation": "...", "evidence": "...", "section_refs": [<section_number>, ...] }

- **section_refs**: The section_number values from the Product Research Results that directly support this CEP situation. Include 1-3 section numbers. If no specific section applies, use an empty array [].
- Return exactly these three keys. Do NOT add any other key.

**7W's Framework** — Use these 7 dimensions as inspiration for diverse CEP situations:
- **Why** (Need/Motivation): The core reason or problem-solving need that makes the consumer recall the category
- **When** (Occasion/Time): The timing or specific occasion that triggers category recall
- **Where** (Location/Context): Where the consumer is, or where they are using the product
- **While** (Parallel Activity): What else the consumer is doing at the same time
- **With Who** (Social Context): Who the consumer is with
- **With What** (Complementary Products): Other products or services paired with this category
- **hoW Feeling** (Emotional State): The consumer's mood or desired emotional state

**Do NOT generate nano-intents.** Do NOT output a "nanoIntents" key, and do NOT write the consumer's purpose as a separate list. The Why dimension is expressed inside the situation sentence itself, grounded in the Product Research Results.

{{evidence_section}}

# Language

**Please respond in {{response_language}} with valid JSON format.**

# Quantity Constraint (CRITICAL)

You MUST return exactly {{requested_count}} CEP objects.
The top-level JSON array length must be exactly {{requested_count}}.

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- Return ONLY a single valid JSON value.
- Do NOT wrap the JSON in Markdown code fences (no ```).
- Do NOT add any prose, explanation, headings, or bullet/numbered lists outside JSON.
- Do NOT add trailing commas.
- Use double quotes for ALL JSON keys and string values.
- Each object has exactly three keys: "situation", "evidence", "section_refs".
- Top-level JSON MUST be an array of length exactly {{requested_count}}.
````
