journey_finder_brand

**v. 25.10.30**

### Instruction

Look at the given search query and judge if it is a 'Brand Keyword'. A 'Brand Keyword' refers to a search query directly related to a specific brand or specific product/model. Even if the word 'brand' is included, if it is used as a common noun and not referring to a specific brand (e.g., combined with rankings, recommendations, types), judge it as a 'General Keyword'.

If the given search query is a 'Brand Keyword', return "True" for "brand"; if 'General Keyword', return "False". If the query is a 'Brand Keyword', extract the part of the search query that is the basis for this judgment for "brand part" and return it. (Return the search query part as is.) If the query is a 'General Keyword', return an empty string for "brand part".

Return only the results without additional explanation.

Brand Keyword:

- Company name (e.g., LG, Samsung)
- Service name (e.g., YouTube, Netflix)
- Brand name (e.g., Galaxy, Gram, iPhone)
- Major product (model) name of a specific brand (e.g., s25, Pro 16)

General Keyword:

- General product category (e.g., smartphone, laptop, washing machine, refrigerator, detergent, cosmetics)
- Common noun (e.g., retinol, apple)
- General keywords containing the word 'brand' (e.g., golf brand rankings, 2025 best brands)

Search Results:

- Search results are referenced only when the meaning of the search query is ambiguous or it is necessary to check for a specific product name, and they are not used to judge brand keyword status based on brand keywords included in the results.
- If the search query itself does not contain a specific brand name, judge it as a General Keyword even if brand names appear in the search results.

### Output Format

{{
"keyword": str -> Copy the 'keyword' value from the input,
"target_kewyord": str -> Copy the 'target keyword' value from the input,
"brand": bool,
"brand_part": str
}}

### Input

%(input)s
