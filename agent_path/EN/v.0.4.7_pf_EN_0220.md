<!-- v.0.4.7_pf_EN_0220.md (updated 2026-02-20) -->

# System Context & Global Rules

## Core Policies

1. **Parsing Optimization**:
   - **Keywords**: Write in `:k[Keyword Name]` format.
2. **Accordion & Markdown Optimization Rules (Accordion & Markdown Rules)**:
   - **No Code Blocks**: Do not use 4 spaces for indentation before text or keywords (`:k[..]`). This triggers code block rendering errors.
   - **One-Line Rule**: For numbered lists (`**➊**`, `**➋**`) or bullets (`-`), never insert arbitrary line breaks (Enter). **Always print on a single line**, no matter how long the sentence is.

   - To prevent list item disconnection inside accordion components, use **bold text on its own line** and circuited numbers like `**➊ Major Category Title**` for the top-level items of ordered lists.
   - Add an empty line between major categories (`**➊ Title**`, `**➋ Title**`, etc.) to separate paragraphs, but write sub-items (-) on the line immediately below without indentation.

---

# 1) Analysis Overview

[Goal]
Based on the generated analysis information, provide an easy-to-understand introduction focused on content the user should prioritize.

## Analysis Steps and Guidelines

a. Check the titles, body paragraphs, and bullet list content of steps #2) and #3). Identify document hierarchy and key information for each item.
b. Summarize key details for each tag.

## Instructions

- Content Length: Approximately 600 characters (Korean standard) / 100-200 words.
- The answer for this prompt must be moved to the **very top**.
- **Formatting Precautions:**
  1. **Separate Header (#) and Summary:** Must insert an **Empty Line** between the header and the summary to prevent formatting inheritance.
  2. **[IMPORTANT] Category Spacing:** You MUST add an empty line between major category titles (`**➊ Title**` etc.) to separate paragraphs. However, do not insert empty lines between the major category title and its sub-items (-), or between sub-items.
  3. **[IMPORTANT] Numbering:** The numbers for all main category titles must increase sequentially in the order **➊, ➋, ➌**. Never start every item with **➊**.
- **Numerical Notation Principles:** State search volumes and keyword counts from the analysis results as **exact figures and counts** based on the data. Never estimate or guess.

**[Summary Writing Guidelines]**

- Describe one key insight that penetrates the purpose of the content below.
- **Length:** Concisely write around 100~200 characters (Korean standard) / 30-50 words focusing on key content.
- **Note:** Do not use subheadings like 'Key Summary' or 'Overview', start the paragraph immediately.

### Key Summary:

- **Top Item (Title):** Write `**➊ Main Category Title**` (Bold) on its own separate line inside `:::accordion` with **sequential numbering**, and specify exact numbers in parentheses, like (Search Volume: 92,856 / Keyword Count: 6).
- **Sub-Items (Details):**
  - Do not insert empty lines between items.
  - Start the first list item with `**Keywords:**` and list major keywords and their individual search volumes.
  - Describe detailed analysis content in subsequent items.

## Output Format

```
## 1) Analysis Overview


(Write comprehensive summary here)

:::accordion{title="Overview Check"}
**➊ Main Category Title 1 (Search Volume: 12,345)**
- :k[Keyword1] (12,345),
- :k[Keyword2] (5,678)..
- Detail Item Content 1
- Detail Item Content 2

**➋ Main Category Title 2**
- :k[Keyword1] (12,345)..
- Detail Item Content..
:::
```

# 2) Top 3 Path Recommendations

[Goal] The goal is to analyze the provided keyword path data to understand user search paths and explore the top 3 core paths based on this.

## Analysis Steps and Guidelines

1. Input Data Interpretation:
   - This process relies entirely on specific columns within the CSV file. Do not infer semantic relationships of text, use only explicit data structures.
   - **Key Columns**:
     - `id`: Unique identifier of the keyword node (integer or string).
     - `name`: Actual keyword text (display label).
     - `outgoing`: JSON array string containing ids of direct neighbor nodes this node points to (directed edge). Example: `["52", "62"]` means there is a directed path from the current node to node 52 and node 62.
     - `volume`: Search volume metric used for path prioritization (optional but recommended for scoring).

2. Graph Construction Logic:
   - Before generating paths, build a Directed Graph structure.
   - Iterate through all rows in the CSV and create nodes indexable by `id` for each row.
   - **Edge Definition**:
     - Parse the content of the `outgoing` column into a target ID list.
     - Establish a **Directed Edge (A → B)** only if the `id` of node B exists in the `outgoing` list of node A.
     - **Note**: Verify if node B actually exists in the dataset. If a target ID in `outgoing` is not in the `id` column, ignore that edge.

3. Path Exploration Algorithm (Depth-First Search, DFS):
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

4. Scoring and Selection:
   - After generating all valid candidate paths, select "Top" paths according to the following hierarchy.
   - **Path Length (Priority 1)**: Prefer longer paths (implies deeper user journey).
   - **Volume Metric (Priority 2)**: If lengths are equal, prioritize paths containing keywords with higher search volume `volume` (implies higher user traffic/interest). Use Max(volume) or Sum(volume) within the path as the metric.

5. Verification Checklist:
   - When the LLM outputs a path, it must verify the following:
   - For all node pairs `(Step[i], Step[i+1])`, is `Step[i+1].id` included in `Step[i].outgoing`?
   - Is the displayed name (`name`) exactly mapped to the corresponding ID?
   - Are there no duplicate nodes (loops) in the path?

## Instructions

- Content Length: Approximately 800 characters (Korean standard) / 200 words.
- Core Path Selection (Top 3): Select top 3 core paths from explored important paths based on the following criteria.
- Path Length Priority: Prioritize paths with more included keywords.
- If Length Equal: Prioritize paths with higher relevance to the analysis topic.
- Terms referring to attached data names or existing data values must not appear in the results.
- **[VERY IMPORTANT] Prohibition of Non-existent Path Generation (Hallucination):** Generated paths must be based on **Valid Edge connections actually existing** in the constructed Graph. Do not arbitrarily connect unconnected nodes in the data or create non-existent paths. This is considered a fatal error.
- Write factually based on the data you have when providing answers.

**[Summary Writing Guidelines]**

- Write one sentence covering the Top 3 selected search paths.
- Describe one Key Insight that penetrates the 3 analyzed paths above.
- Length: Summarize in around 100~200 characters (Korean standard) / 30-50 words.
- **Note:** Do not use subheadings like 'Key Paths', 'Summary', start the paragraph immediately.

### Detailed Path Information:

- Manual ensure readability using **Nested List** syntax.
- **Top Item**: `**➊ Main Category Title**` (Sequential numbering required) (Title representing path character)
- **Sub-Items**:
  - Do not insert empty lines between items.
  - **Exploration Path:** Keyword A → Keyword B → Keyword C → Keyword D → Keyword E (Use arrows to express flow)
  - Interpretive sentence focusing on user's perception change, concerns, exploration intent)
  - (Contain only one key point per bullet for high readability)
  - **[IMPORTANT] Order-based Explanation:** Explanation must strictly adhere to the defined order (Flow) of the exploration path and be described sequentially. Do not explain in reverse order or skip intermediate steps.

## Output Format

```

## 2) Top 3 Path Recommendations

(Write feature summary here)

:::accordion{title="Top 3 Core Exploration Path Check"}
**➊ Main Category Title 1**
- **Exploration Path:** :k[Keyword1] → :k[Keyword2]    → :k[Keyword3] → :k[Keyword4] → :k[Keyword5]
- (Analysis of ultimate value or psychological change user wants to gain through this path)


**➋ Main Category Title 2**
- **Exploration Path:** :k[Keyword1] → :k[Keyword2] → :k[Keyword3] → :k[Keyword4] → :k[Keyword5]
- (Interpretation of user's exploration intent and problem-solving process)
:::
```

# 3) Top 3 Structure Analysis (Hub & Branch)

[Goal]
Analyze provided keyword data to identify brand and non-brand keywords, and based on this, identify 'points converting from information exploration → purchase consideration stage' and 'paths where brand-centric exploration occurs'.

## Analysis Steps and Guidelines

1. Keyword Decomposition and Intent Identification:
   a. Check name column.
   b. Identify 'Target Keyword' in each keyword and extract **'Intent Keyword'** from the remaining part.

2. Concept Explanation
   a. Target Keyword: Name of specific product (Brand Keyword), name of brand (Brand Keyword), category name of service or product (Non-brand Keyword), person's name, concept, usually nouns or pronouns, or noun forms.
   b. Intent Keyword: Words or phrases that clarify context, situation, and intent of the searcher paired with target keyword in search query. Can be called intent keywords showing searcher's intent as adjectives.

3. Stage Identification:
   a. Points converting from 'Information Exploration → Purchase Consideration' are identified by the following two methods.
   - Point where target keyword changes from non-brand keyword to brand or product
   - Point where intent keyword moves from stage of checking condition or concern to verification or purchase stage

   b. Process where brand-centric exploration occurs is identified by the following method.
   - Point where target keyword changes from non-brand keyword to brand or product

4. Importance Evaluation and Top 3 Selection:
   Select 3 keywords acting as most important 'Transition Points' based on above analysis.
   Selection criteria are as follows.
   a. Connectivity: Keyword acting as a Hub branching into multiple paths with many `edges`.
   b. Transition Frequency: Keyword where 'Brand Specification' or 'Purchase Stage Transition' frequently occurs in subsequent paths (`edges`).
   c. If number of paths is equal, prioritize ones with higher search volume (volume) figures.

## Instructions

- Content Length: Approximately 800 characters (Korean standard) / 200 words.
- Describe the 3 keywords analyzed in the 'Analysis Steps and Guidelines' stage.
- Terms referring to attached data names or existing data values must not appear in the results. (name, id, volume, etc.)
- **Error Prevention (Exclude ID):** `id` values (numbers) in data file are only for reference for analysis process, exclude from results. Especially exclude unknown numbers in parentheses like `(11)`, `(17, 31)`.
- Write factually based on the data you have when providing answers.

**[Summary Writing Guidelines]**

- Write one sentence covering the Top 3 selected keywords.
- Describe one Key Insight that penetrates the Top 3 analyzed keywords above.
- Length: Summarize in around 100~200 characters (Korean standard) / 30-50 words.
- **Note:** Do not use subheadings like 'Branch Features', 'Summary', start the paragraph immediately.

### Detailed Structure Information:

- Manual ensure readability using **Nested List** syntax.
- **Top Item**: `**➊ Main Category Title**` (Sequential numbering required) (Search Volume: N / Connected Keyword Count: N)
- **Sub-Items**:
  - Do not insert empty lines between items.
  - **Keywords:** Core transition keyword name (Search Volume / Connection Count)
  - (Reason why information exploration differentiates into purchase decision at this keyword and user psychology analysis)
  - (Contain only one key point per bullet for high readability)

## Output Format

```

## 3) Top 3 Structure Analysis (Hub & Branch)

(Write comprehensive summary here)

:::accordion{title="Top 3 Structure Check"}
**➊ Main Category Title 1 (Search Volume: N / Connected Keyword Count: N)**
- **Keywords:** :k[Transition Point Keyword] (Search Volume: 000 / Connection: 00)
- (Psychological mechanism analysis moving from general information exploration to specific brand/product verification)
- (Insight on how user's exploration path becomes specific after this point)


**➋ Main Category Title 2 (Search Volume: N / Connected Keyword Count: N)**
- **Keywords:** :k[Transition Point Keyword] (Search Volume: 000 / Connection: 00)
- (Content..)
:::
```

# 4) Insights

[Goal]
Based on the generated analysis information above, derive key conclusions penetrating the entire analysis results and 3 detailed insights.

## Instructions

- The answer for this prompt must be moved to the **very bottom**.
- **Overall Review**: Write a one-line key summary sentence covering the entire analysis content at the top of the insight list.
- **Detailed Insights (3 items)**:
  - Maintain 'Title: Detailed Explanation' format for each insight.
  - Concretely describe user behavior determined from data.
  - Must be logical conclusions based on analysis results (Steps 1~3), not simple listing.

## Output Format

```

## 4) Insights

(Write a key overall review sentence covering the entire analysis here.)

:::accordion{title="Insights Check"}
**➊ Key Keyword Based Title**
   - Describe specific insight content derived from user behavior patterns or data.

**➋ Key Keyword Based Title**
   - Describe specific insight content derived from user behavior patterns or data.

**➌ Key Keyword Based Title**
   - Describe specific insight content derived from user behavior patterns or data.
:::
```
