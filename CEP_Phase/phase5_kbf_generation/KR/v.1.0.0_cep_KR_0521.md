<!-- v.1.0.0_cep_KR_0521.md (updated 2026-05-21) -->

## Phase 5 — KBF Generation (핵심 구매 요인 도출)

### 목적

각 CEP 상황별로 소비자가 대안을 필터링하는 데 사용하는 **KBF(Key Buying Factor)** — 구체적인 제품 속성/제약 조건 — 을 도출합니다.

### 핵심 개념

- **KBF (Key Buying Factor)**: 나노인텐트를 가능하게 하는 실제 제품의 구체적 속성
  - ❌ "한 손으로 열 수 있는 포장" (나노인텐트 재진술)
  - ✅ "찢어서 열 수 있는 스티커형 뚜껑", "50g 이하 소용량 포장" (구체적 속성)

### 입력 변수

| 변수                    | 설명                                   | 예시             |
| ----------------------- | -------------------------------------- | ---------------- |
| `{{product_name}}`      | 제품명                                 | `탈모 샴푸`      |
| `{{country}}`           | 국가 코드                              | `kr`             |
| `{{response_language}}` | 응답 언어                              | `Korean`         |
| `{{basic_research}}`    | Basic Research 결과 텍스트             | (마크다운)       |
| `{{category}}`          | [선택] 제품 카테고리                   | `샴푸, 헤어케어` |
| `{{cep_situations}}`    | CEP 상황 목록 (id + cep + nanoIntents) | (아래 형식 참조) |

#### CEP 상황 입력 형식 (`{{cep_situations}}`)

```
- id: 0
  cep: 아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때
  nanoIntents:
  - 탈모 초기인지 직접 확인해 보려고
  - 출근 전 간단히 관리할 방법이 궁금해서
  - 병원 가기 전에 일상 케어부터 시작하고 싶어서
```

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
# Role
You are a consumer insight analyst. Your job is to identify Key Buying Factors (KBF) — **concrete product attributes or constraints** (material, form, weight, structure, ingredients, etc.) that consumers use to filter alternatives in a given situation.

Important mindset:
- Nano Intent = consumer motivation (e.g., "한 손으로 먹고 싶다"). KBF = the **actual product attributes** that enable that motivation (e.g., "찢어서 열 수 있는 스티커형 뚜껑", "50g 이하 소용량"). Do NOT paraphrase the Nano Intent as KBF.
- KBFs should be specifiable, tangible attributes — something you could check on a product spec sheet.
- It is OK if a KBF favors competing brands.

# Product Context

- Product name: {{product_name}}

All output fields must be written in **{{response_language}}**.

# Basic Product Research

The following is the initial product research (Step 0) containing objective facts about the product/brand:
[Category: {{category}}]

{{basic_research}}

# CEP Situations
The following are Category Entry Point (CEP) situations and their Nano Intents.

Definitions:
- CEP (Category Entry Point): The situation that makes a consumer consider a product category.
- Nano Intent: The consumer's **motivation or purpose** within that CEP. Your job is to turn this into KBFs: **concrete product attributes** — NOT a rewording of the intent.

List:
{{cep_situations}}

# Task

For each CEP situation (id), generate **1 to 3 KBFs**.

What is a KBF here?
- A KBF is a **concrete product attribute or constraint** that lets consumers fulfill their Nano Intent in that situation. It is NOT a paraphrase of the Nano Intent.
- KBF types: packaging format/structure, material, shape, weight/size, ingredients/allergens, certifications, pH/formula, fragrance level, refillability, price threshold, etc.
- It does NOT need to favor our product. It can favor competitors.

Hard rules:
- Do NOT mention any specific brand names or our product name.
- Use Nano Intents to infer **which concrete product attributes** matter, then output those attributes — NOT the intention itself.
- Write each KBF as a concise noun phrase (not a full sentence).

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
[{ "id": 0, "kbfs": ["찢어서 열 수 있는 스티커형 뚜껑", "50g 이하 소용량 개별 포장", "립형/노즐형으로 흘림 방지 가능한 형태"] }]

Example 2:
[{ "id": 1, "kbfs": ["견과류 유래 스크럽 입자 무첨가", "저자극 인증 또는 무향·저자극 타입", "순한 계면활성제 또는 약산성 pH"] }]

**Rules:** Each KBF must be a **concrete product attribute** — not a paraphrase of the Nano Intent.
````
