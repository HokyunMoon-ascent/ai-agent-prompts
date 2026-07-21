<!-- v.0.1.8_keyword_compare_path_0721.md (updated 2026-07-21) -->

- (Response language) Regardless of the data's language, always write the answer in the language of {{locale}}. (KR=Korean, JP=Japanese, US/EN=English)

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

# 0-1) Path Generation and Verification Process

1. **Input Data Interpretation**:
   - This process relies entirely on specific columns within the CSV file. Do not infer semantic relationships of text, use only explicit data structures.
   - **Key Columns**:
     - `id`: Unique identifier of the keyword node (integer or string).
     - `name`: Actual keyword text (display label).
     - `outgoing`: JSON array string containing ids of direct neighbor nodes this node points to (directed edge). Example: `["52", "62"]` means there is a directed path from the current node to node 52 and node 62.
     - `volume`: Search volume metric used for path prioritization (optional but recommended for scoring).

2. **Graph Construction Logic**:
   - Before generating paths, build a Directed Graph structure.
   - Iterate through all rows in the CSV and create nodes indexable by `id` for each row.
   - **Edge Definition**:
     - Parse the content of the `outgoing` column into a target ID list.
     - Establish a **Directed Edge (A → B)** only if the `id` of node B exists in the `outgoing` list of node A.
     - **Note**: Verify if node B actually exists in the dataset. If a target ID in `outgoing` is not in the `id` column, ignore that edge.

3. **Path Exploration Algorithm (Depth-First Search, DFS)**:
   - To discover meaningful exploration paths, perform traversal (DFS recommended) observing the following rules.
   - **Traversal Rules**:
     - **Start Point**: All nodes in the graph can be potential start points.
     - **Expansion**: Moving from current node A to node B is possible only if B is in A's `outgoing` list.
     - **Cycle Prevention**: Do not revisit nodes already present in the current path sequence (e.g., A → B → A is invalid).
     - **Path Length**:
       - **Min Length**: 5 nodes (Basic criteria for broad exploration).
       - **Max Length**: (Optional, e.g., 10) To prevent excessive depth.
   - **Step-by-step Logic (Pseudocode)**:
     ```
     Initialize Variable: PathList = []
     For each node "StartNode" in Graph:
         Stack = [(StartNode, [StartNode])]
         While Stack is not empty:
             (Current, Path) = Stack.pop()
             If Length(Path) >= 5: Append Path to PathList
             For each NeighborID in Current.outgoing:
                 If NeighborID not in Path:
                     NewPath = Path + [NeighborID]
                     Stack.push((Node(NeighborID), NewPath))
     ```

4. **Scoring and Selection**:
   - After generating all valid candidate paths, select "Top" paths according to the following hierarchy.
   - **Path Length (Priority 1)**: Prefer longer paths (implies deeper user journey).
   - **Volume Metric (Priority 2)**: If lengths are equal, prioritize paths containing keywords with higher search volume `volume` (implies higher user traffic/interest). Use Max(volume) or Sum(volume) within the path as the metric.

5. **Verification Checklist**:
   - When the LLM outputs a path, it must verify the following:
   - For all node pairs `(Step[i], Step[i+1])`, is `Step[i+1].id` included in `Step[i].outgoing`?
   - Is the displayed name (`name`) exactly mapped to the corresponding ID?
   - Are there no duplicate nodes (loops) in the path?

# 1) Analysis Overview

**[Guide]** Key summary focusing on differences in exploration paths between A and B.

- **Length:** 100~200 characters including spaces (Start with plain text without subheadings).
- **Position:** Top. Observe Header/Dropdown separation rule.
- **Numerals:** Exact numerical notation is Essential.
- **Data Cleaning:** **Must remove** `(ID)` (e.g., `(25)`) attached after keywords in raw data and intent markers `(C), (I)`, output only pure text.

### Key Summary:

- **Top Item (Title):** Write in the format `**➊ Main Category Title 1**` (Bold) inside `:::accordion` on a separate line and specify exact numbers in parentheses, like (Search Volume: 92,856 / Keyword Count: 6).
  - Describe detailed comparison points (Commonalities/Differences).

## Output Format

```
## 1) Analysis Overview
(Comprehensive Summary)

:::accordion{title="Overview Check"}
**➊ Main Category Title 1 (Search Volume: 92,856 / Keyword Count: 6)**
- **Keywords**: :k[Keyword1](N), :k[Keyword2](N)...
- Detailed Comparison Content 1
- Detailed Comparison Content 2

**➋ Main Category Title 2 ...**
- Detailed Content...
:::
```

# 2) [Keyword A] Top 3 Path Recommendations

**[Guide]** Explore top 3 main search paths based on `keyword_a_data`.

- **Length:** About 400 characters (approx. 100 words).
- **Data-based description essential**

**[Analysis Logic]**

1. **Filter**: Explore relevant paths only (Exclude: DIFF, DEF, JOB, FIN, PERSON).
2. **Path Exploration (Top 3)**:
   - **Method**:
     1. Can start from any `id`.
     2. Expand to adjacent keywords along `edges` (`id` → `neighbor` → ...).
     3. Repeat until **5 or more** keywords are consecutive.
   - **Constraint**: Do not visit duplicate `id` within the same path (Cycle prevention).
3. **Priority**:
   - Priority 1: **Length (Longest)** (Number of included keywords).
   - Priority 2: **Search Volume (Highest Volume)** (If lengths equal).
4. **Output Cleaning**: **Must delete** `(ID)` information (e.g., `(25)`) and intent markers `(C), (I)` after keywords, output only pure text.

**[Summary]** A exploration pattern features (100~200 characters).

### Detailed Information Structuring

- **Top:** `**➊ Main Category Title**` (Path Character)
- **Sub:**
  - `Exploration Path:` K1 → K2 → ...
  - Exploration Intent/Needs Analysis.

## Output Format

```
## 2) [Keyword A] Top 3 Path Recommendations
(Feature Summary)

:::accordion{title="[Keyword A] Core Exploration Path Check"}
**➊ Main Category Title 1 (Search Volume: 12,345 / Keyword Count: 5)**
- **Exploration Path:** :k[K1] → :k[K2] → :k[K3]...
- (Value/Intent Analysis)

**➋ Main Category Title 2 ...**
- (Content...)
:::
```

# 3) [Keyword B] Top 3 Path Recommendations

**[Guide]** Explore top 3 main search paths based on `keyword_b_data`.

- **Length:** About 400 characters (approx. 100 words).
- **Logic:** Same as #2) (Filter/Path Definition/Priority).

**[Summary]** B exploration pattern features (100~200 characters).

### Detailed Information Structuring

- **Top:** `**➊ Main Category Title**`
- **Sub:**
  - `Exploration Path:` K1 → K2 → ...
  - Exploration Intent/Needs Analysis.

## Output Format

```
## 3) [Keyword B] Top 3 Path Recommendations
(Feature Summary)

:::accordion{title="[Keyword B] Core Exploration Path Check"}
**➊ Main Category Title 1 (Search Volume: 12,345 / Keyword Count: 5)**
- **Exploration Path:** :k[K1] → :k[K2] → :k[K3]...
- (Value/Intent Analysis)

**➋ Main Category Title 2 ...**
- (Content...)
:::
```

# 4) Path Comparative Analysis (A vs B)

**[Guide]** Analysis of Needs Gap and Graph Character through A/B main path comparison.

- **Length:** About 400 characters (approx. 100 words).
- **Method:** Use Contrast technique ("While A is ~, B is ~").
- **Data Cleaning**: **Must remove** `(ID)` information and intent markers like `(C), (I)` from keyword names.

**[Analysis Points]**

1. **Graph Character**: Deep & Narrow vs Shallow & Wide, Goal differences.
2. **Diff Detail**: Exploration Intent (Info Acquisition vs Purchase Comparison), Interest Topic (Spec vs Price) differences.
3. **Insight**: Differentiation points based on perception differences between groups.

**[Summary]** One sentence insight on decisive difference (100~200 characters).

### Detailed Information Structuring

- **Top:** `**➊ Comparative Analysis Topic**`
- **Sub:** [A] Features, [B] Features, Implications.

## Output Format

```
## 4) Path Comparative Analysis (A vs B)
(Comparison Summary)

:::accordion{title="Path Comparative Analysis Check"}
**➊ Difference in Exploration Tendency (Search Volume: 100,000 / Keyword Count: 10)**
- **:k[Keyword A]:** ...
- **:k[Keyword B]:** ...
- **Insight:** ...

**➋ Main Category Title 2 ...**
- (Content...)
:::
```

# 5) Insights

**[Guide]** Derive conclusion and implications.

- **Position:** Bottom
- **Structure:** Overall Review (1 line) + Detailed Insights (3 items, Title:Content).

## Output Format

```
## 5) Insights
(Key Overall Review)

:::accordion{title="Insights Check"}
**➊ Title**: Content
**➋ Title**: Content
**➌ Title**: Content
:::
```

## Context Data

```csv
{{context_csv}}
```

{{target_prompt}}

{{target_topic_prompt}}

{{prev_conversation_prompt}}

##Current_question
{{user_question}}
