<!-- v.1.0.0_cep_EN_0706.md (updated 2026-07-06) -->

## 8. Phase 5-1 — KBF User Prompts (AI chatbot example question generation)

### Purpose

Based on the CEP, nano-intent, and KBF metadata, generate **9 natural questions (3 per KBF)** that real users would likely type into an AI chatbot.

### Input Variables

| Variable                      | Description                                                             | Example                           |
| ----------------------------- | ---------------------------------------------------------------------- | --------------------------------- |
| `{{cep}}`                     | CEP situation (`card.cep`)                                             | `아침 샤워 후 머리 빠질 때`       |
| `{{nano_intent}}`             | Nano Intent (`card.nano_intent`)                                       | `탈모 초기 자가 진단`             |
| `{{kbf}}`                     | String joining all KBFs of the card with `", "` (`", ".join(kbf_list)`) | `약산성 pH 제품, 무향, 저자극`    |
| `{{response_language_label}}` | Response language label (based on locale KR/JP/US, default Korean)      | `Korean` / `Japanese` / `English` |

### Output Format

JSON array (exactly 9 items — 3 per KBF × 3 KBFs)

```json
[
  "질문1",
  "질문2",
  "질문3",
  "질문4",
  "질문5",
  "질문6",
  "질문7",
  "질문8",
  "질문9"
]
```

### Request Model and Parameters

| Parameter | Value                                        | Note                                        |
| --------- | -------------------------------------------- | ------------------------------------------- |
| model     | `'gpt-5.4-nano'`                             | Fixed                                       |
| input     | Result string of `buildKbfUserPromptsPrompt(...)` | Reflects CEP / Nano Intent / KBF / output language |
| text      | `{ format: { type: 'text' } }`               | Plain text output                           |
| reasoning | `{ effort: 'none' }`                         | Minimal reasoning effort                    |

### Code Location

- Constant: `KBF_USER_PROMPTS_TEMPLATE`
- Code location: `app/module/clients/cep_prompts.py:963`
- Calling API: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt Template

```
Using the CEP (Category Entry Point), Nano Intent, and KBFs (Key Buying Factors) below, generate natural questions that real users would ask AI chatbots (ChatGPT, Gemini, Perplexity, etc.).
Focus on high-intent, compound prompts that commonly appear in AI search.
Tone: Write in a friendly, casual, approachable style—as if chatting with a helpful friend.
Provide exactly 9 items (3 questions per KBF) as a list with no additional explanation.

Input:
- CEP (Category Entry Point): {{cep}}
- Nano Intent: {{nano_intent}}
- KBFs (Key Buying Factors): {{kbf}}

Output language: {{response_language_label}}

Response format (output ONLY a JSON array, nothing else):
["question1", "question2", "question3", "question4", "question5", "question6", "question7", "question8", "question9"]
```
