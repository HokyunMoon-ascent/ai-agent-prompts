<!-- v.1.0.0_cep_EN_0521.md (updated 2026-05-21) -->

## Phase 5 — KBF Generation (Deriving Key Buying Factors)

### Purpose

For each CEP situation, derive the **KBF (Key Buying Factor)** — the concrete product attributes/constraints — that consumers use to filter alternatives.

### Key Concepts

- **KBF (Key Buying Factor)**: The concrete attribute of the actual product that enables a nano-intent
  - ❌ "한 손으로 열 수 있는 포장" (restatement of the nano-intent)
  - ✅ "찢어서 열 수 있는 스티커형 뚜껑", "50g 이하 소용량 포장" (concrete attributes)

### Input Variables

| Variable                | Description                            | Example          |
| ----------------------- | -------------------------------------- | ---------------- |
| `{{product_name}}`      | Product name                           | `탈모 샴푸`      |
| `{{country}}`           | Country code                           | `kr`             |
| `{{response_language}}` | Response language                      | `Korean`         |
| `{{basic_research}}`    | Basic Research result text             | (markdown)       |
| `{{category}}`          | [Optional] Product category            | `샴푸, 헤어케어` |
| `{{cep_situations}}`    | CEP situation list (id + cep + nanoIntents) | (see format below) |

#### CEP Situation Input Format (`{{cep_situations}}`)

```
- id: 0
  cep: 아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때
  nanoIntents:
  - 탈모 초기인지 직접 확인해 보려고
  - 출근 전 간단히 관리할 방법이 궁금해서
  - 병원 가기 전에 일상 케어부터 시작하고 싶어서
```

### Output Format

JSON array (same length as the number of CEPs)

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

### Request Model and Parameters

| Parameter         | Value                                                  |
| :---------------- | :----------------------------------------------------- |
| model             | 'gpt-5.4-nano'                                         |
| input             | buildKBFGenerationPrompt(...) result or promptOverride |
| text              | { format: { type: 'text' }, verbosity: 'low' }         |
| reasoning         | { effort: 'none', summary: null }                      |
| tools             | []                                                     |
| store             | false                                                  |
| include           | []                                                     |
| max_output_tokens | 32768                                                  |

---

### Prompt Template

````
# Role
You are a consumer insight analyst. Your job is to identify Key Buying Factors (KBF) — **concrete product attributes or constraints** (material, form, weight, structure, ingredients, etc.) that consumers use to filter alternatives in a given situation.

Important mindset:
- Nano Intent = consumer motivation (e.g., "한 손으로 먹고 싶다"). KBF = the **actual product attributes** that enable that motivation (e.g., "찢어서 열 수 있는 스티커형 뚜껑", "소용량 1회분 개별 포장"). Do NOT paraphrase the Nano Intent as KBF.
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

**Rules:** Each KBF must be a **concrete product attribute** — not a paraphrase of the Nano Intent, and **not a fabricated numeric value or range**. Quote a specific number only if it is explicitly present in the Basic Product Research.
````
