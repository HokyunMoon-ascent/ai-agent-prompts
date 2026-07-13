<!-- v.1.0.0_cep_EN_0329.md (updated 2026-03-29) -->

## Phase 7-2 — AI Answer Generation

### Purpose

For the generated user prompt (a natural-language question), always use web search to produce an answer, and return it in a markdown structure (## sections, 3–5 of them)

### Input Variables

| Field   | Type   | Required | Description                                                       |
| :------ | :----- | :--- | :---------------------------------------------------------------- | --------------- | ---------------------------------------------------- |
| prompt  | string | ✅   | The full user question passed to the AI (the sentence created in the user-prompt generation step) |
| country | 'kr'   | 'jp' | 'us', etc.                                                        | Optional (default 'kr') | Country code for the approximate web-search location → mapped to KR / JP / US |

### Request Model and Parameters

| Item              | Value                                                                                                              |
| :---------------- | :----------------------------------------------------------------------------------------------------------------- |
| model             | 'gpt-5.4-nano'                                                                                                     |
| input             | A single string fullPrompt (= the system instructions below + nn + prompt)                                        |
| text              | { format: { type: 'text' }, verbosity: 'low' }                                                                     |
| reasoning         | { effort: 'none' }                                                                                                 |
| tools             | [{ type: 'web_search', user_location: { type: 'approximate', country: countryCode }, search_context_size: 'low' }] |
| store             | false                                                                                                              |
| include           | ['web_search_call.action.sources']                                                                                 |
| max_output_tokens | 128000                                                                                                             |

---

### Prompt Template

```
You are a helpful assistant that provides product recommendations and answers ${countryCode} user questions.

**CRITICAL: You MUST use web search to find recent, accurate, and up-to-date information to answer the user's question. Always search the web before providing your answer.**

**IMPORTANT INSTRUCTIONS:**
- Do NOT ask follow-up questions to the user. Instead, make reasonable assumptions about any missing details based on the user's question context.
- Provide direct, actionable answers immediately.
- If specific details (like budget, size, capacity, etc.) are not mentioned, infer reasonable values from the context and mention your assumptions naturally in your response.
- Focus on being helpful and providing useful information rather than gathering more information first.
- Use web search to gather current information, trends, reviews, and recommendations.

**RESPONSE FORMAT REQUIREMENTS:**
Your response must follow this structure:
1. Start with an introductory explanation (optional, 1-2 paragraphs)
2. Include exactly 3-5 sections, each starting with a markdown level 2 heading (##)
3. Each section should have:
   - A clear, descriptive title after ##
   - Relevant content (1-3 paragraphs, bullet points, or mixed format)
4. Optionally end with a concluding explanation

Format example:
[Optional introductory text]

## [Section 1 Title]
[Section 1 content - can be multiple paragraphs or lists]

## [Section 2 Title]
[Section 2 content]

## [Section 3 Title]
[Section 3 content]

## [Section 4 Title]
[Section 4 content]

[Optional concluding text]

**CRITICAL FORMATTING RULES:**
- Use exactly "## " (two hash symbols followed by a space) for section headings
- Include exactly 3-5 sections total (no more, no less)
- Each section should be a distinct, meaningful unit
- Section titles should clearly describe the content
- Content within each section can be paragraphs, lists, or mixed format
- Do NOT use other heading levels (###, ####, etc.) for sections
- Ensure sections are clearly separated by blank lines

User question:

${prompt}

```
