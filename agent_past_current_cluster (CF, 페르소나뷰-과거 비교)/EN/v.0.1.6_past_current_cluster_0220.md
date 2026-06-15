<!-- v.0.1.6_past_current_cluster_0220.md (updated 2026-06-01) -->

# System Context & Global Rules

## Core Policies

1. **Parsing Optimization**:
   - **Keywords**: Write in `:k[Keyword Name]` format.
   - **Clusters**: Write in `:c[Cluster ID]{#ID}` format. (e.g., `:c[Cluster A]{#A}`)
2. **Accordion & Markdown Optimization Rules (Accordion & Markdown Rules)**:
   - **No Code Blocks**: Do not use 4 spaces for indentation before text or keywords (`:k[...]`). This triggers code block rendering errors.
   - **One-Line Rule**: For numbered lists (`**➊**`, `**➋**`) or bullets (`-`), never insert arbitrary line breaks (Enter). **Always print on a single line**, no matter how long the sentence is.

   - To prevent list item disconnection inside accordion components, use **bold text on its own paragraph** and circuited numbers like `**➊ Major Category Title**` for the top-level items of ordered lists.
   - Add an empty line between major categories (`**➊ Title**`, `**➋ Title**`, etc.) to separate paragraphs, but write sub-items (-) on the line immediately below without indentation.

# 1) Analysis Overview

**[Guide]** Summarize the pattern of change in Search Intent between Past and Current.

- **Length:** 100~200 characters including spaces (Plain text).
- **Position:** Top.
- **Numerals:** Exact numerical notation is Essential. (Use Past data for Past content, Current data for Current content).

### Key Summary:

- **Top Item (Title):** Write in the format `**➊ Main Category Title 1**` (Bold) inside `:::accordion` on a separate line and specify exact numbers in parentheses, like (Search Volume: 92,856 / Keyword Count: 6).
  - Mention change status (Rising/Falling/Sustaining).
  - Describe detailed content.

## Output Format

```
## 1) Analysis Overview
(Write comprehensive summary here)

:::accordion{title="Overview Check"}
**➊ Main Category Title 1 (Search Volume: 92,856 / Keyword Count: 6)**
- **Keywords**: :k[Keyword1](N), :k[Keyword2](N)...
- Detail Item Content (Change Point) 1
- Detail Item Content (Change Point) 2

**➋ Main Category Title 2 ...**
- Detailed Content...
:::
```

# 2) Past Top 3 Search Intent Analysis

**[Guide]** Analyze top 3 major search intents in the past based on `path_persona_compare_data.past` data (`graph`, `info`).

- **Length:** About 800 characters (approx. 200 words).
- **Focus:** Focus on the context of the **Past** (Refrain from comparing with Current).

**[Analysis Logic]**

1. **Hub Keyword Identification**:
   - Refer to `hub_keywords` data. Select cluster candidates with existing values (`["Keyword"]`).
   - If multiple keywords exist, use all for intent interpretation.
2. **Intent-based Grouping**:
   - **Intent Extraction**: Extract `Intent`(Adjective/Verb) excluding Hub's `Target`(Noun/Brand).
   - **Grouping**: Merge similar Intent clusters.
3. **Verification**: Group must include `hub_keywords` identified cluster.
4. **Priority**: Top 3 based on sum of search volume (`volume`) within group.
5. **Data Matching**: Use `path_persona_compare_data.past.info.["Keyword"]` data when mentioning keywords in the explanation body (**Do not display search volume**).

**[Summary]** Insight into main search intent in the past (100~200 characters).

### Detailed Information Structuring

- **Top:** `**➊ Search Intent Title** (Included Cluster Count: N)`
- **Sub:**
  - `Clusters with same search intent: [A, B]`
  - Mainstream reasons for search intent at the time, consumer psychology.

## Output Format

```
## 2) Past Top 3 Search Intent Analysis
(Feature Summary)

:::accordion{title="Past Search Intent Check"}
**➊ Search Intent Title 1 (Cluster Count: NN)**
- **Clusters with same search intent:** :c[A]{#A}, :c[B]{#B}
- (Main consumer interests in the past)

**➋ Search Intent Title 2 ...**
- (Content...)
:::
```

# 3) Current Top 3 Search Intent Analysis

**[Guide]** Analyze top 3 major search intents in the current period based on `path_persona_compare_data.current` data (`graph`, `info`).

- **Length:** About 800 characters (approx. 200 words).
- **Focus:** Focus on the context of the **Current**. (Interpret how past contexts have continued).

**[Analysis Logic]** Same as #2). (However, use `path_persona_compare_data.current.info.["Keyword"].volume` data).

**[Summary]** Insight into main search intent in the current period.

### Detailed Information Structuring

- Same structure as #2).

## Output Format

```
## 3) Current Top 3 Search Intent Analysis
(Feature Summary)

:::accordion{title="Current Search Intent Check"}
**➊ Search Intent Title 1 (Total Search Volume: NN / Cluster Count: NN)**
- **Clusters with same search intent:** :c[A]{#A}, :c[B]{#B}
- (Main consumer interests in the present)

**➋ Search Intent Title 2 ...**
- (Content...)
:::
```

# 4) Search Intent Shift Comparison (Past vs Current)

**[Guide]** Analyze Needs Shift through comparison of Past/Current major intents.

- **Length:** About 800 characters (approx. 200 words).
- **Method:** Focus on 'Flow of Change' (Sustaining/Rising/Falling).

**[Analysis Points]**

1. **Matching**: Check for maintenance, new emergence, disappearance.
2. **Factor**: Specification (segmentation), correlation with external factors (trends/seasons).
3. **Insight**: Derive pattern "Info Acquisition (Past) -> Action/Purchase (Current)".

**[Summary]** Insight penetrating the flow of change.

### Detailed Information Structuring

- **Top:** `**➊ Key Topic of Change**`
- **Sub:**
  - `Change Aspect:` [Past] A → [Current] B
  - Inference of background and psychology.

## Output Format

```
## 4) Search Intent Shift Comparison (Past vs Current)
(Change Summary)

:::accordion{title="Search Intent Shift Analysis Check"}
**➊ Changed Search Intent Pattern Title 1 (Search Volume: 50,000 / Keyword Count: 5)**
- **Change Aspect:** [Past] Interest A Centered → [Current] Interest B Centered
- (Consumer Needs Evolution Process)

**➋ Changed Search Intent Pattern Title 2 ...**
- (Content...)
:::
```
