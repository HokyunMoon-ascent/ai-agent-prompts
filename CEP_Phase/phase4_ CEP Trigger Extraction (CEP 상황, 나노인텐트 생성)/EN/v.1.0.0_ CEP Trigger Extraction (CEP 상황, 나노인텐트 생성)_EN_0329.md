<!-- v.1.0.0_cep_EN_0329.md (updated 2026-03-29) -->

## Phase 4 — CEP Trigger Extraction (CEP situations, nano-intent generation)

### Purpose

Extract **concrete CEP (Category Entry Point) situations and nano-intents** from the Product Research results.  
Each situation must have diversity guaranteed based on the 7W's Framework.

### Core Concepts

- **CEP (Category Entry Point)**: A concrete situation/trigger that makes a consumer need or recall a specific product category
- **Nano Intent (nano-intent)**: The concrete purpose that diverges from person to person within the same CEP (= Why dimension, exactly 3)
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why

### Input Variables

| Variable                        | Description                                      | Example            |
| ------------------------------- | ----------------------------------------------- | ------------------ |
| `{{product_name}}`              | Product name                                     | `탈모 샴푸`        |
| `{{country}}`                   | Country code                                      | `kr`               |
| `{{product_research_sections}}` | CEP Insight Research result sections            | (markdown text)    |
| `{{requested_count}}`           | Number of CEP situations to extract              | `10`               |
| `{{category}}`                  | [Optional] Product category                      | `샴푸, 헤어케어`   |
| `{{existing_situations}}`       | [Optional] Existing CEP list (for dedup)         | (already generated list) |

### Output Format

JSON array (exactly `{{requested_count}}` items)

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

### Request Model and Parameters

| Parameter         | Value                                                               | Note                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | Fixed                              |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | Single user message + input_text part |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  |                                    |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt Template

````
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

Each situation: { "situation": "...", "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "evidence": "...", "section_refs": [<section_number>, ...] }

- **section_refs**: The section_number values from the Product Research Results that directly support this CEP situation. Include 1-3 section numbers. If no specific section applies, use an empty array [].

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
- Top-level JSON MUST be an array of length exactly {{requested_count}}.
```
````
