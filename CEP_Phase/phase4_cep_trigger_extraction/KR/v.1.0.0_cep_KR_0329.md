<!-- v.1.0.0_cep_KR_0329.md (updated 2026-03-29) -->

## Phase 4 — CEP Trigger Extraction (CEP 상황, 나노인텐트 생성)

### 목적

Product Research 결과에서 **구체적인 CEP(Category Entry Point) 상황과 나노인텐트**를 추출합니다.  
각 상황은 7W's Framework 기반으로 다양성이 보장되어야 합니다.

### 핵심 개념

- **CEP (Category Entry Point)**: 소비자가 특정 제품 카테고리를 필요로 하거나 떠올리게 되는 구체적 상황/트리거
- **나노인텐트 (Nano Intent)**: 동일 CEP 안에서 사람마다 갈라지는 구체적 목적 (=Why 차원, 정확히 3개)
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why

### 입력 변수

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
    "nanoIntents": [
      "탈모 초기인지 직접 확인해 보려고",
      "출근 전 간단히 관리할 방법이 궁금해서",
      "병원 가기 전에 일상 케어부터 시작하고 싶어서"
    ],
    "evidence": "2"
  }
]
```

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
# Role
You are a consumer behavior analyst specializing in Category Entry Point (CEP) identification.
Your expertise is in uncovering the real-life situations, triggers, and contexts that lead consumers to think of or need a specific product category.

[선택] # Category
The following categories describe the product/brand context. Use them to ground CEP situations in the right category scope.

**Categories:** {{category}}

# Product Research Results
---
Section 1: {{section_1_title}}

{{section_1_content}}

---
Section 2: {{section_2_title}}

{{section_2_content}}

[... 추가 섹션들 ...]

# CEP Definition

CEP (Category Entry Point): The Situation/Trigger
CEP refers to a specific situation, context, or cue that makes consumers need or consider purchasing a specific product or service. For example, "when thirsty", "when at a movie theater", "when needing to buy a gift for a friend" are exactly those situations.

# Task

[기존 CEP가 없는 경우]
Based on the Product Research Results above, identify {{requested_count}} real-life situations where consumers would think of or need '{{product_name}}'. Provide them as a list.

[기존 CEP가 있는 경우]
**IMPORTANT: You have already generated {{existing_count}} CEP situations. Below are the existing situations:**

{{existing_situations_list}}

**Your task is to generate {{requested_count}} NEW CEP situations that are CLEARLY DIFFERENT from the existing ones above.**
- Do NOT rephrase or slightly modify existing situations
- Avoid similar emotional states, social contexts, or usage moments
- Actively search for NEW, unexplored contexts
- If a situation feels like it could belong to an existing CEP cluster, discard it and move on
Think: "What situations have NOT been named yet?"

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

[{{requested_count}} >= 12인 경우]
Across the entire output list of {{requested_count}} situations, aim to include at least:
- **When**: 2+
- **Where**: 2+
- **While**: 2+
- **With Whom**: 2+
- **With What**: 1+
- **hoW Feeling**: 2+

[{{requested_count}} >= 8인 경우]
Across the entire output list of {{requested_count}} situations, aim to include at least:
- **When**: 2+, **Where**: 1+, **While**: 1+, **With Whom**: 1+, **With What**: 1+, **hoW Feeling**: 1+

[그 외]
Try to span **at least {{min(requested_count, 4)}} different 7W dimensions** across the full list.

# Format

Each situation: { "situation": "...", "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "evidence": "..." }

**7W's Framework** — Use these 7 dimensions as inspiration for diverse CEP situations:
- **Why** (Need/Motivation): The core reason or problem-solving need → Expressed as nanoIntents
- **When** (Occasion/Time): The timing or specific occasion that triggers category recall
- **Where** (Location/Context): Where the consumer is, or where they are using the product
- **While** (Parallel Activity): What else the consumer is doing at the same time
- **With Who** (Social Context): Who the consumer is with
- **With What** (Complementary Products): Other products or services paired with this category
- **hoW Feeling** (Emotional State): The consumer's mood or desired emotional state

**Nano Intent (nanoIntents array, exactly 3 items)**: Concrete purposes that differ per consumer within the same CEP (= Why dimension).
- Do NOT repeat product name or category
- Avoid generic phrases like "need it", "ran out of it"
- Output exactly 3 nanoIntents per situation

Examples:
[When] "아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때" → ["탈모 초기인지 직접 확인해 보려고", "출근 전 간단히 관리할 방법이 궁금해서", "병원 가기 전에 일상 케어부터 시작하고 싶어서"]
[Where] "드럭스토어 헤어케어 코너에서 제품을 고르는 중" → ["성분 기반으로 효과 있는 제품을 직접 비교하려고", "약국 전용과 일반 제품의 차이가 궁금해서", "두피 타입에 맞는 제품을 현장에서 골라보고 싶어서"]

# Evidence (Section References) — REQUIRED

[섹션이 있는 경우]
**You MUST include evidence for EVERY situation.** Each situation must reference the most relevant SECTION from the Product Research Results above.
**Evidence Format:** Single string (e.g., "2" for Section 2).

[섹션이 없는 경우]
**The "evidence" field must be set to null.**

# Output Examples

```json
[
  { "situation": "<concrete situation>", "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "evidence": "<section number>" }
]
````

**Note:** Every situation must include an "evidence" field.

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
- Top-level JSON MUST be an array of length exactly {{requested_count}}.

```

```
