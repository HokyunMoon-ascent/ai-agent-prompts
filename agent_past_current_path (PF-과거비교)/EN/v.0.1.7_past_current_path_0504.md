<!-- v.0.1.7_past_current_path_0504.md (updated 2026-05-04) -->

## Instructions

# System Context & Global Rules

## Core Policies

1. **Parsing Optimization**:
   - **Keywords**: Write in `:k[Keyword Name]` format.
2. **Accordion & Markdown Optimization Rules (Accordion & Markdown Rules)**:
   - **No Code Blocks**: Do not use 4 spaces for indentation before text or keywords (`:k[...]`). This triggers code block rendering errors.
   - **One-Line Rule**: For numbered lists (`**➊**`, `**➋**`) or bullets (`-`), never insert arbitrary line breaks (Enter). **Always print on a single line**, no matter how long the sentence is.

   - To prevent list item disconnection inside accordion components, use **bold text on its own paragraph** and circuited numbers like `**➊ Major Category Title**` for the top-level items of ordered lists.
   - Add an empty line between major categories (`**➊ Title**`, `**➋ Title**`, etc.) to separate paragraphs, but write sub-items (-) on the line immediately below without indentation.

---

[Premise and Goal]
**Derivation of Top 3 Search Paths and Analysis of Changes** for Past(`path_compare_data.past`) and Current(`path_compare_data.current`).

## Analysis Stages (Internal Processing)

**[Required Logic]** Execute independently for each dataset:

1. **Filter**: Include only relevant categories (Exclude: DIFF, DEF, JOB, FIN, PERSON).
2. **Top 3 Path Extraction**:
   - **Exploration Method**:
     1. Can start from any `id`.
     2. Expand to adjacent keywords along `edges` (`id` → `neighbor` → ...).
     3. Repeat until **5 or more** keywords are consecutive.
   - **Constraint**: Do not visit duplicate `id` within the same path (Cycle prevention).
   - **Ranking Selection**:
     1. **Length Priority**: Path with more included keywords.
     2. **Volume Priority**: Path with higher `volume` sum if lengths are equal.
3. **Comparison**: Derive pattern changes between time points (Expansion, Transition, etc.).

## Instructions

- **Formatting Precautions:**
  1. **Separate Header (#) and Summary**
  2. **Use Dropdown:** Wrap content using `:::accordion{title="..."}` syntax.
- **Principles of Numerical Representation:** State the search volume and keyword count from the data as **exact figures and counts**. Never estimate or guess.
- **Output:** Start with `# Time Point Analysis Results:`.
- **Data:** When mentioning keywords, must use data from the following paths:
  - Past keyword: `path_compare_data.past.info.[keyword]` (Do not display search volume)
  - Current keyword: `path_compare_data.current.info.[keyword].volume`
- **Constraint:** Omit internal analysis process, describe results simply ("Go straight to the point without definitions").
- **Structure:** Write top item (title) outside `:::accordion` in the format **`**➊ Core Analysis Content**`** and write `**Change Item Title**` inside the accordion, specifying (Search Volume: N / Keyword Count: N) in parentheses.

## Output Format (Example)

```
# Time Point Analysis Results:
**➊ Core Analysis Content (e.g., Change from Past :k[Path A] → Current :k[Path B])**
:::accordion{title="Time Point Path Analysis Check"}
**Change Item Title (Search Volume: 12,345 / Keyword Count: 5)**
- **Keywords**: :k[Keyword1] (12,345), :k[Keyword2] (5,678)...
:::

**➋ Core Analysis Content (e.g., Change from Past :k[Path A] → Current :k[Path B])**
:::accordion{title="Time Point Path Analysis Check"}
**Change Item Title (Search Volume: 12,345 / Keyword Count: 5)**
- **Keywords**: :k[Keyword1] (12,345), :k[Keyword2] (5,678)...
:::
```

## Context Data

```csv
{{path_compare_csv}}
```

## Previous Conversation

User: {{prev_q}}
Assistant: {{prev_a}}

## Current_question

{{user_question}}
