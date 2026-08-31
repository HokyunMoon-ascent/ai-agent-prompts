<!-- v.1.1.0_phase5-1_AI 챗봇 예시 질문 생성_EN_0814.md (updated 2026-08-14) -->

## 8. Phase 5-1 — KBF User Prompts (AI chatbot example question generation)

### Purpose

Based on the CEP and KBFs, generate **4 natural questions** that real users would likely type into an AI chatbot.

The composition of the 4 questions is fixed.

| No. | Composition   | Description                                                        |
| --- | ------------- | ------------------------------------------------------------------ |
| 1   | CEP only      | The CEP situation itself turned into a question. No KBF mentioned.  |
| 2   | CEP × KBF 1   | The CEP situation with the first KBF added as a condition           |
| 3   | CEP × KBF 2   | The CEP situation with the second KBF added as a condition          |
| 4   | CEP × KBF 3   | The CEP situation with the third KBF added as a condition           |

> Change history
> - v.1.1.0 (0814): Output changed from 9 items (3 per KBF) to **4 items (1 CEP-only + 3 CEP×KBF)**. Each question's role is fixed by position.
>   ⚠ Backend alignment required: response array length check 9 → 4.
> - v.1.0.0 (0714): Nano intent was removed from the Phase 4 output, so the input variable `{{nano_intent}}` was removed.
>   ⚠ Backend alignment required: remove the nano_intent argument from `buildKbfUserPromptsPrompt(...)`.

### Input Variables

| Variable                      | Description                                                             | Example                           |
| ----------------------------- | ---------------------------------------------------------------------- | --------------------------------- |
| `{{cep}}`                     | CEP situation (`card.cep`)                                              | `아침 샤워 후 머리 빠질 때`       |
| `{{kbf}}`                     | String joining all KBFs of the card with `", "` (`", ".join(kbf_list)`) | `약산성 pH 제품, 무향, 저자극`    |
| `{{response_language_label}}` | Response language label (based on locale KR/JP/US, default Korean)      | `Korean` / `Japanese` / `English` |

**The KBF order is the question order.** Since `{{kbf}}` is a `", "`-joined string, the join order (the index order of `kbf_list`) maps directly to questions 2, 3, and 4. If the backend reorders the KBFs, the question order changes with it.

### Output Format

JSON array (exactly 4 items)

```json
[
  "Question turning the CEP itself into a question",
  "CEP × KBF1 question",
  "CEP × KBF2 question",
  "CEP × KBF3 question"
]
```

If there are fewer than 3 KBFs: output only `1 + number of KBFs` items (2 KBFs → 3 items). If there are more than 3 KBFs, use only the first 3.

### Request Model and Parameters

| Parameter | Value                                             | Note                                 |
| --------- | ------------------------------------------------- | ------------------------------------ |
| model     | `'gpt-5.4-nano'`                                  | Fixed                                |
| input     | Result string of `buildKbfUserPromptsPrompt(...)` | Reflects CEP / KBF / output language |
| text      | `{ format: { type: 'text' } }`                    | Plain text output                    |
| reasoning | `{ effort: 'none' }`                              | Minimal reasoning effort             |

### Code Location

- Constant: `KBF_USER_PROMPTS_TEMPLATE`
- Code location: `app/module/clients/cep_prompts.py:963`
- Calling API: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt Template

```
<!-- v.1.1.0_phase5-1_AI 챗봇 예시 질문 생성_EN_0814.md -->
Using the CEP (Category Entry Point) and KBFs (Key Buying Factors) below, generate 4 natural questions that real users would ask AI chatbots (ChatGPT, Gemini, Perplexity, etc.).
Focus on high-intent prompts that commonly appear in AI search.
Tone: Write in a friendly, casual, approachable style—as if chatting with a helpful friend.

Input:
- CEP (Category Entry Point): {{cep}}
- KBFs (Key Buying Factors): {{kbf}}

The KBFs are a `", "`-separated list. In order from the front, they are KBF1, KBF2, and KBF3.

The composition of the 4 questions is fixed in the following order. Do not change the order.
1. Turn the CEP situation itself into a question. Do not mention any KBF.
2. A question that adds KBF1 as a condition to the CEP situation
3. A question that adds KBF2 as a condition to the CEP situation
4. A question that adds KBF3 as a condition to the CEP situation

Writing rules:
- Each question covers exactly one KBF. Do not mix two or more KBFs into one question.
- Do not paste the KBF wording verbatim—work it in naturally, the way a user would actually phrase it.
- All four questions must stay within the same CEP situation. Do not change it or invent a new one.
- Do not include brand names or product names.
- Vary the sentence structure across questions so the same pattern does not repeat.
- If there are fewer than 3 KBFs, create only as many as exist, for a total of (1 + number of KBFs) items. If there are more than 3 KBFs, use only the first 3.

Output language: {{response_language_label}}

Response format (output ONLY a JSON array, nothing else):
["question1", "question2", "question3", "question4"]
```
