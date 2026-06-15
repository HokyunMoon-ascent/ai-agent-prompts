<!-- v.0.2.2_agent_persona_specialist_EN_0220.md (updated 2026-06-01) -->

The above is the search keyword cluster information that groups `%(query)s` (search keywords) confirmed from the search sequence data of keywords that a group of people searched successively to solve a certain purpose or concern.

`High Volume Keywords` (search keywords) are keywords with high importance in the cluster, and `Top Frequency URLs` (search results) is the information listing the words that appeared commonly on the search result pages confirmed by searching each search keyword, ordered by frequency.

By synthesizing the two types of information given above, please analyze the persona of the people who searched for these keywords. In the persona analysis, deeply estimate what situation the people who searched for these keywords are in, or what desire they have, or what concern or problem they are trying to solve, or what they were curious about, and find up to 3 personas with the highest probability from here.

When estimating the persona, find a concrete persona rather than a vague one by utilizing the 'search keywords' used by the consumer when searching and the 'search results' of these keywords as much as possible. If there is a keyword that mentions a specific product or brand among the search keywords, actively utilize this information to estimate a specific persona, such as a person interested in that brand or product rather than a person interested in the entire category.

If you have analyzed the persona in the above way, but no persona is found, please write 'No persona found.'.

Next, for each persona list, create a list of 3 questions that this persona would have asked by guessing what the persona was actually curious about while searching. However, for the question list, use only bullets (-) and do not use numbers.

When analyzing the persona, if there is no specific gender or age in the search keywords, you do not need to guess a persona that specifies age or gender. In particular, if the value of `g` (gender) or `a` (age) in the provided data is `0`, it means the data does not exist, so absolutely do not specify gender or age.

The number starts from 1, and list number 1 is the persona with the highest priority.
Each persona must include an 'Analysis Summary', 'Rationale', and a 'Question List'. Both 'Rationale' and 'Question List' should be included inside an accordion (`:::accordion`).

## Output Format

The 'Analysis Summary' should be written concisely, synthesizing the persona's situation and desires.
When writing the rationale, you must specifically specify the core keywords or search result contents that led to estimating the persona. However, absolutely do not arbitrarily fabricate numerical values such as search volume (Volume) that are not in the provided data.
When mentioning keywords, absolutely do not include numbers (IDs) for data identification, such as '(Keyword 172)'.

** Accordion & Markdown Optimization Rules (Accordion & Markdown Rules) **

- **No Code Blocks**: Do not use 4 spaces for indentation before text or keywords (`:k[...]`). This triggers code block rendering errors.
- **One-Line Rule**: For numbered lists (`**➊**`, `**➋**`) or bullets (`-`), never insert arbitrary line breaks (Enter). **Always print on a single line**, no matter how long the sentence is.

- To prevent list item disconnection inside accordion components, use **bold text on its own paragraph** and circuited numbers like `**➊ Major Category Title**` for the top-level items of ordered lists.
- Add an empty line between major categories (`**➊ Title**`, `**➋ Title**`, etc.) to separate paragraphs, but write sub-items (-) on the line immediately below without indentation.

```

## Persona 1 : ex) A woman worried about health, ~ person, ~ consumer

(Write Analysis Summary)

:::accordion{title="Rationale Check"}
**➊ Core Keywords and Intent**
   - **Keywords**: :k[Keyword1] (12,345), :k[Keyword2] (5,678)...
   - Detail Item Content 1
   - Detail Item Content 2

**➋ Analysis Content**
   - **Keywords**: :k[Keyword1] (12,345)...
   - Detail Item Content...

**➌ Question List**
   - ~
   - ~
   - ~
:::

## Persona 2 :

(Write Analysis Summary)

:::accordion{title="Rationale Check"}
**➊ Core Keywords and Intent**
   - **Keywords**: :k[Keyword1] (12,345), :k[Keyword2] (5,678)...
   - Detail Item Content 1
   - Detail Item Content 2

**➋ Analysis Content**
   - **Keywords**: :k[Keyword1] (12,345)...
   - Detail Item Content...

**➌ Question List**
   - ~
   - ~
   - ~
:::

## Persona 3 :

(Write Analysis Summary)

:::accordion{title="Rationale Check"}
**➊ Core Keywords and Intent**
   - **Keywords**: :k[Keyword1] (12,345), :k[Keyword2] (5,678)...
   - Detail Item Content 1
   - Detail Item Content 2

**➋ Analysis Content**
   - **Keywords**: :k[Keyword1] (12,345)...
   - Detail Item Content...

**➌ Question List**
   - ~
   - ~
   - ~
:::
```
