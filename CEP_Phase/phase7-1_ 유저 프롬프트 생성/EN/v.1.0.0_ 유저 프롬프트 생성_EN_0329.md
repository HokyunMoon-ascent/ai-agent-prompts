<!-- v.1.0.0_cep_EN_0329.md (updated 2026-03-29) -->

## Phase 7-1 — User Prompt Generation

### Purpose

Generate one question per CEP by inserting Nano Intent, KBF, and category (natural language for AI search)

### Input Variables

| Field             | Type     | Required               | Server-side processing         |
| :---------------- | :------- | :--------------------- | :----------------------------- | --------- | ------------------------------------------------------------------------ |
| cepSituation      | string   | ✅                     | Trim whitespace; 400 if empty  |
| nanoIntents       | string[] | Default []             | Strings only, trim, max 3      |
| kbfs              | string[] | ✅ (must not be empty) | Strings only, trim, max 3      |
| productCategories | string[] | Default []             | Strings only, trim             |
| country           | 'kr'     | 'jp'                   | 'us', etc.                     | Default 'kr' | Used as a fallback when responseLocale is absent                       |
| responseLocale    | 'ko'     | 'en'                   | 'ja'                           | Optional  | If present, takes priority in deciding lang (en/ja as-is; otherwise ja/en/ko by country) |

### Request Model and Parameters

| Item      | Value                                                                                 |
| :-------- | :----------------------------------------------------------------------------------- |
| model     | 'gpt-5.4-nano'                                                                       |
| input     | Single string from buildCepUserPromptsPrompt(...) (passed as input whole, without separating into a user message) |
| text      | { format: { type: 'text' } }                                                         |
| reasoning | { effort: 'none' }                                                                   |

---

### Prompt Template

```
Using the CEP (Category Entry Point) situation, Nano Intents, and KBFs (Key Buying Factors) below, generate a natural question that real users would ask AI chatbots (ChatGPT, Gemini, Perplexity, etc.).

**IMPORTANT RULES:**
1. Transform the given CEP situation naturally.
2. Generate exactly ONE comprehensive question that naturally incorporates ALL the KBFs provided below.
3. The question should include the CEP situation context and weave together all Key Buying Factors in a natural, conversational way.
4. The product category should be naturally integrated.
5. Do NOT use technical product specifications or exact KBF terminology. Instead, describe the features in everyday language that a regular user would naturally use.
6. Write from the perspective of a regular consumer who doesn't know exact product features but knows what they need in their situation.
7. Use natural, conversational language—as if chatting with a helpful friend.
8. Multiple buying factors should flow naturally in one cohesive question, not as a disconnected list.

**Example:**
- CEP Situation: When I need to take care of my breakfast before going to work
- Nano Intents: "Maintain crispiness", "Make it quickly", "Making cooking routine"
- KBF 1: "Tempered glass door"
- KBF 2: "Quick heating technology"
- KBF 3: "Easy-clean coating"

- Bad: "When I need to take care of my dinner after work, I'm looking for an air fryer with a tempered glass door, quick heating, and easy cleaning."
  -  ❌ Completely Changed CEP situation.
  -  ❌ Used technical product specifications or exact KBF terminology.
  -  ❌ Listed features without natural flow.

- Good: "I need to take care of my breakfast before going to work during a busy preparation time. I want an air fryer that heats up fast so I can cook quickly, lets me see inside clearly to check doneness in real time, and is easy to wipe clean afterwards. What products are available?"
  - ✅ Includes CEP situation ("take care of my breakfast before going to work")
  - ✅ Includes Nano Intents ("during a busy preparation time", "cook quickly")
  - ✅ Includes ALL KBF focuses naturally ("heats up fast", "see inside clearly", "easy to wipe clean") without technical terms
  - ✅ Includes product category ("air fryer")
  - ✅ Multiple needs flow naturally in one cohesive question

The question must tell a complete story: the user's situation (given CEP), what they want to achieve (Nano Intents), and what product features they need (all KBFs naturally woven together).
Focus on high-intent, compound prompts that commonly appear in AI search.
Tone: Write in an informal, friendly, casual, approachable style—as if chatting with a helpful same age person.

Input:
- CEP Situation: ${cepSituation}
- Nano Intents:
${nanoIntentsText}
- KBFs (Key Buying Factors) - ALL must be included in the question:
${kbfsText}
- Product Categories: ${productCategoriesText}

Output language: ${langLabel}

Response format (output ONLY a JSON array with ONE question, nothing else):
["comprehensive question that includes all KBFs"]
```
