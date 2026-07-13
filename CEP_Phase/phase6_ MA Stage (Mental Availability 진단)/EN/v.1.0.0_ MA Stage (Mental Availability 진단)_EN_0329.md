<!-- v.1.0.0_cep_EN_0329.md (updated 2026-03-29) -->

## Phase 6 — MA Stage (Mental Availability Diagnosis)

### Purpose

Diagnose a specific brand's **Mental Availability (MA) across 6 stages** from AI Overview responses.  
Evaluate multiple AIO documents for the same CEP in batch.

### MA Stage 6-Stage Definitions

| Stage              | Description                                  |
| ------------------ | ------------------------------------- |
| `Absent`           | Neither brand nor category mentioned         |
| `Category Only`    | Category is mentioned but the brand is not |
| `Competitor Owned` | Only competitors mentioned; analysis target brand absent  |
| `Mentioned`        | Included in the candidate set but with low prominence     |
| `Compared`         | Appears in a pros/cons comparison context             |
| `Recommended`      | Prioritized recommendation for the given CEP              |

### Input Variables

| Variable                    | Description                        | Example                      |
| ----------------------- | --------------------------- | ------------------------- |
| `{{brand_name}}`        | Analysis target brand name          | `로보락 S8`               |
| `{{cep_trigger_cue}}`   | CEP purchase context               | `아파트 1인 가구 청소 시` |
| `{{nano_intent}}`       | Specific purpose                   | `빠른 청소`               |
| `{{kbf}}`               | Key buying factor (KBF)              | `자동 먼지 비움 기능`     |
| `{{aio_documents}}`     | AIO document array (id + text) | (see format below)          |
| `{{document_count}}`    | Number of AIO documents               | `5`                       |
| `{{response_language}}` | Rationale writing language              | `Korean`                  |

#### AIO Document Format (`{{aio_documents}}`)

```
### Document 1 (ID: "doc-abc123")

```

[AI Overview response text — up to 1000 characters]

```

### Document 2 (ID: "doc-def456")

```

[AI Overview response text]

```

```

### Output Format

JSON array (exactly `{{document_count}}` elements)

```json
[
  {
    "id": "doc-abc123",
    "ma_stage": "Mentioned",
    "rationale": "분석 대상 브랜드가 후보군에 포함되어 있으나 비중이 낮음."
  }
]
```

### Request Model and Parameters

| Field              | Value                                            |
| :---------------- | :-------------------------------------------- |
| model             | gpt-5.4-nano                                  |
| input             | systemPrompt + "nn" + userInput (single string) |
| text              | { format: { type: 'text' } }                  |
| reasoning         | { effort: 'none' }                            |
| max_output_tokens | 2048                                          |

---

### Prompt Template

````
# Role
You are an expert in diagnosing brand Mental Availability from AI Overview responses.
Your task is to evaluate how prominently a specific brand appears in each AI response within a given purchase context (CEP).
You will receive multiple AI Overview documents and must judge each one independently.

# MA Stage (6 levels)

1. **Absent**: Neither the brand nor the product category is mentioned (category not recognized)
2. **Category Only**: Product category is mentioned, but the brand does not appear
3. **Competitor Owned**: Only competitors are mentioned; the analysis target brand does not appear
4. **Mentioned**: Included in the candidate set but with low prominence
5. **Compared**: Appears in comparison context with strengths/weaknesses described
6. **Recommended**: Prioritized recommendation for the given CEP

# Judgment Principles
- Verify whether the analysis target brand appears and in what context
- **Product(Brand) matching**: Treat variants generally recognized as the same product(brand) as valid
- Consider relevance to CEP, Nano Intent, and KBF
- The same mention can map to different stages depending on context
- When uncertain, choose the lower (more conservative) stage

# Independent Evaluation (CRITICAL)
- Evaluate EACH AI Overview document **completely independently**.
- The judgment for one document must NOT influence any other document's judgment.
- Apply the same criteria consistently across all documents.
- Do NOT skip any document. Every input document must have a corresponding output entry.

# Boundary Case Guide

| Boundary | → Lower stage | → Higher stage |
|----------|--------------|----------------|
| Category Only / Competitor Owned | No brand named at all | Any competitor brand named |
| Mentioned / Compared | Flat list, no evaluation | Any strength/weakness/attribute stated for the brand |
| Compared / Recommended | Pros/cons without preference | Explicit recommendation for the given CEP |

Special cases:
- **Negative mention**: counts as Mentioned; with comparative detail → Compared; never Recommended
- **CEP-irrelevant mention**: cap at Mentioned regardless of depth

# Output Format

Return ONLY a valid JSON **array** with exactly **{{document_count}}** elements (one per input document).
Each element must have exactly three keys:
- **id**: The document ID (must match the input document's ID exactly)
- **ma_stage**: One of "Absent" | "Category Only" | "Competitor Owned" | "Mentioned" | "Compared" | "Recommended"
- **rationale**: 1–2 sentences explaining the judgment (in the language specified below)

# Output Rules (STRICT, JSON-ONLY)
- Return ONLY a single valid JSON **array**.
- The array must contain exactly **{{document_count}}** elements.
- Each element's **id** must exactly match the corresponding input document's ID.
- Do NOT wrap the JSON in Markdown code fences (no ```).
- Do NOT add any prose, explanation, or headings outside the JSON.
- Use double quotes for ALL JSON keys and string values.

# Language
- Write the **rationale** field in **{{response_language}}**

# Input

- **Analysis target brand**: {{brand_name}}
- **CEP (purchase context)**: {{cep_trigger_cue}}
- **Nano Intent (specific purpose)**: {{nano_intent}}
- **KBF (key buying factor)**: {{kbf}}

## AI Overview Responses ({{document_count}} documents)

{{aio_documents}}

Analyze each AI Overview document above independently and output the MA Stage for each as a JSON array.
```
````
