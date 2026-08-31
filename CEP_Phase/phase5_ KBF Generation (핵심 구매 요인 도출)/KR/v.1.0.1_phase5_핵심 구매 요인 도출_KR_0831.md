<!-- v.1.0.1_phase5_핵심 구매 요인 도출_KR_0831.md (updated 2026-08-31) -->
<!-- 베이스: v.1.0.0_ KBF Generation (핵심 구매 요인 도출)_KR_0521.md
     이번 개정(v.1.0.1)에서 바꾼 것은 나노인텐트 참조 제거 하나뿐입니다. 그 외 문구는 v.1.0.0 그대로입니다.
     ⚠ 백엔드 정합 필요: cep_situations 조립 시 nanoIntents 주입 제거 (Phase 4 v.1.0.1과 짝) -->

## Phase 5 — KBF Generation (핵심 구매 요인 도출)

### 목적

각 CEP 상황별로 소비자가 대안을 필터링하는 데 사용하는 **KBF(Key Buying Factor)** — 구체적인 제품 속성/제약 조건 — 을 도출합니다.

### 핵심 개념

- **KBF (Key Buying Factor)**: CEP 상황에서 소비자가 대안을 필터링할 때 사용하는 실제 제품의 구체적 속성
  - ❌ "한 손으로 열 수 있는 포장" (소비자 니즈의 재진술)
  - ✅ "찢어서 열 수 있는 스티커형 뚜껑", "50g 이하 소용량 포장" (구체적 속성)

> 나노인텐트가 Phase 4 출력에서 제거됨에 따라, 본 프롬프트의 입력·정의에서 나노인텐트 참조를 제거하고 KBF를 CEP 상황 기반으로 재정의했습니다.
> v.1.0.0 문서는 머리말에서 제거를 선언해 놓고 템플릿에는 Nano Intent 정의·지시가 그대로 남아 있었습니다. v.1.0.1은 그 모순을 없앤 판본입니다.

### 개정 이력

| 버전    | 날짜  | 변경                                                                                                        |
| ------- | ----- | ----------------------------------------------------------------------------------------------------------- |
| v.1.0.1 | 08-31 | 템플릿의 Nano Intent 정의·판정 기준·"Use Nano Intents to infer" 지시를 CEP 상황 기준으로 교체. 그 외 무변경 |
| v.1.0.0 | 05-21 | 최초 판본                                                                                                   |

### 입력 변수

v.1.0.0과 동일합니다. **신규 변수 0개 · 삭제 변수 0개.**

| 변수                    | 설명                       | 예시             |
| ----------------------- | -------------------------- | ---------------- |
| `{{product_name}}`      | 제품명                     | `탈모 샴푸`      |
| `{{country}}`           | 국가 코드                  | `kr`             |
| `{{response_language}}` | 응답 언어                  | `Korean`         |
| `{{basic_research}}`    | Basic Research 결과 텍스트 | (마크다운)       |
| `{{category}}`          | [선택] 제품 카테고리       | `샴푸, 헤어케어` |
| `{{cep_situations}}`    | CEP 상황 목록 (id + cep)   | (아래 형식 참조) |

#### CEP 상황 입력 형식 (`{{cep_situations}}`)

```
- id: 0
  cep: 아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때
```

`nanoIntents` 줄은 넣지 않습니다. Phase 4 v.1.0.1이 그 필드를 반환하지 않습니다.

### 출력 형식

JSON 배열 (CEP 개수와 동일한 길이)

```json
[
  {
    "id": 0,
    "kbfs": [
      "약산성 pH 5.5 제품",
      "두피 각질 제거 성분 포함",
      "무향 또는 저자극 인증"
    ]
  }
]
```

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                 |
| :---------------- | :----------------------------------------------------- |
| model             | 'gpt-5.4-nano'                                         |
| input             | buildKBFGenerationPrompt(...) 결과 또는 promptOverride |
| text              | { format: { type: 'text' }, verbosity: 'low' }         |
| reasoning         | { effort: 'none', summary: null }                      |
| tools             | []                                                     |
| store             | false                                                  |
| include           | []                                                     |
| max_output_tokens | 32768                                                  |

---

### Prompt 템플릿

````
<!-- v.1.0.1_phase5_핵심 구매 요인 도출_KR_0831.md (updated 2026-08-31) -->
# Role
You are a consumer insight analyst. Your job is to identify Key Buying Factors (KBF) — **concrete product attributes or constraints** (material, form, weight, structure, ingredients, etc.) that consumers use to filter alternatives in a given situation.

Important mindset:
- The CEP describes the consumer's situation (e.g., "한 손밖에 쓸 수 없는 출근길"). KBF = the **actual product attributes** that resolve that situation (e.g., "찢어서 열 수 있는 스티커형 뚜껑", "소용량 1회분 개별 포장"). Do NOT paraphrase the situation as KBF.
- KBFs should be specifiable, tangible attributes — something you could check on a product spec sheet.
- It is OK if a KBF favors competing brands.

# Product Context

- Product name: {{product_name}}

All output fields must be written in **{{response_language}}**.

# Basic Product Research

The following is the initial product research (Step 0) containing objective facts about the product/brand:
{{category_line}}

{{basic_research}}

# CEP Situations
The following are Category Entry Point (CEP) situations.

Definitions:
- CEP (Category Entry Point): The situation that makes a consumer consider a product category. Your job is to turn this situation into KBFs: **concrete product attributes** — NOT a rewording of the situation.

List:
{{cep_situations}}

# Task

For each CEP situation (id), generate **1 to 3 KBFs**.

What is a KBF here?
- A KBF is a **concrete product attribute or constraint** that resolves the consumer's blocker in that situation. It is NOT a paraphrase of the situation.
- KBF types: packaging format/structure, material, shape, weight/size, ingredients/allergens, certifications, pH/formula, fragrance level, refillability, price threshold, etc.
- It does NOT need to favor our product. It can favor competitors.

Hard rules:
- Do NOT mention any specific brand names or our product name.
- Read the CEP situation to infer **which concrete product attributes** matter, then output those attributes — NOT the situation itself.
- Write each KBF as a concise noun phrase (not a full sentence).
- Do NOT invent specific numeric values or ranges (weights, angles, sizes, pH, percentages, temperatures, battery hours, DPI, etc.) such as "100–150g", "20°~40°", "pH 5.5", "50g 이하". Describe the attribute **qualitatively** instead (e.g., "경량 설계", "낮은 경사 각도", "약산성 pH", "소용량 1회분"). Only include an exact figure if it is **explicitly stated in the Basic Product Research above** — never fabricate or estimate.

Output language: Write KBF strings in **{{response_language}}**.

# Output Format

## JSON Structure

Return a JSON array. Each element is an object with two keys: `id` and `kbfs`.

[
  {
    "id": 0,
    "kbfs": ["<kbf 1>", "<kbf 2>", "<kbf 3>"]
  }
]

## Output Rules (STRICT, JSON-ONLY)

- Return ONLY a single valid JSON array value. No Markdown code fences (no ```).
- No prose, explanations, or lists outside the JSON.
- No trailing commas. Use double quotes for all keys and string values.
- Array length must match the number of CEP situations provided (1:1 by id).
- `id` must be a number (0-based).
- `kbfs` must be an array of 1 to 3 strings.

# Examples ({{response_language}})

These examples are ONLY for understanding. Do NOT include them in the final output.

Example 1:
[{ "id": 0, "kbfs": ["찢어서 열 수 있는 스티커형 뚜껑", "소용량 1회분 개별 포장", "립형/노즐형으로 흘림 방지 가능한 형태"] }]

Example 2:
[{ "id": 1, "kbfs": ["견과류 유래 스크럽 입자 무첨가", "저자극 인증 또는 무향·저자극 타입", "순한 계면활성제 또는 약산성 pH"] }]

**Counter-examples (DO NOT produce these):**
- ❌ "저용량·경량 설계 무게(예: 100–150g)" — fabricated numeric range
- ❌ "세미 버티컬(경사 각도 표기, 예: 20°~40°) 형태" — fabricated numeric range
- ✅ instead: "저용량·경량 설계", "세미 버티컬(완만한 경사 각도) 형태"

**Rules:** Each KBF must be a **concrete product attribute** — not a paraphrase of the CEP situation, and **not a fabricated numeric value or range**. Quote a specific number only if it is explicitly present in the Basic Product Research.
````
