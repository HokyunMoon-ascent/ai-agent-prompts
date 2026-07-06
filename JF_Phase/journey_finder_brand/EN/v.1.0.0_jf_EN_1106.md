journey_finder_brand

**v. 25.11.06**

### Instruction

Look at the given [Target Keyword] and judge if this search query is a 'Brand Keyword' referring to a brand name, product name, or service name. A 'Brand Keyword' refers to a search query that includes a specific brand name or a specific product/model name. It can be a single word, but it can also take the form of a noun phrase, clause, or sentence with adjectives or other nouns. In other words, any search query that includes a specific brand name or a specific product/model name can be called a brand keyword.

Keywords combined with rankings, recommendations, types, etc., within a specific brand's products (e.g., LG refrigerator recommendation, Samsung Electronics TV types) should also be judged as brand keywords, even if LG refrigerator or Samsung Electronics implies a category.

In the Output Format, you must return "True" for "brand" if the given search query is a 'Brand Keyword', and "False" for "brand" if it is a 'General Keyword'. If the query is a 'Brand Keyword', extract the keyword within the query that is the basis for this judgment as the "brand part" and return it. (Return the brand-corresponding keyword from the query as is.) If the query is a 'non-brand general keyword', return an empty string for "brand part".

The input [Seed Keywords] sometimes include non-brand keywords, but they also include brand keywords such as major brand names, product names, and service names for that product/service category.

Return only the results without additional explanation.

Brand Keyword:

- Company name (e.g., LG, Samsung)
- Service name (e.g., YouTube, Netflix)
- Brand name (e.g., Galaxy, Gram, iPhone)
- Product (model) name (e.g., s25, Pro 16, "M874GBB151" which is a model number for an LG refrigerator)

General Keyword (Non-brand Keyword):

- General product/service category (e.g., smartphone, delivery app, laptop, washing machine, refrigerator, travel app, detergent, cosmetics)
- Common noun (e.g., retinol, apple, hyaluronic acid, collagen)
- General keywords containing the word 'brand' (e.g., golf brand rankings, 2025 best brands)

[Search Result Page Content]:

- Viewing the [Search Result Page Content] is helpful when it is ambiguous whether a search query is a brand keyword or a common noun. For example, "Apple" is a common noun but is now a brand. "Toss" was also a foreign verb but is now a remittance app brand. Refer to the [Search Result Page Content] when it is ambiguous whether the search query is a brand keyword or not, or when it is necessary to check if it is a specific product name or model number.
- If the search query itself does not contain a specific brand name, judge it as a non-brand keyword even if brand names appear in the search results.

### Output Format

{{
"keyword": str -> Copy the 'keyword' value from the input,
"target_kewyord": str -> Copy the 'target keyword' value from the input,
"brand": bool,
"brand_part": str
}}

### Input

[Target Keyword]  
%(input)s

[Seed Keyword]  
%(keyword)s

[Search Result Page Content]  
%(serp)s

(input) = keyword, target keyword, and serp data are entered.  
for keyword in batch:  
 brand_input_str += f”keyword: {keyword}n”  
 brand_input_str += f”target keyword: {target_map[keyword]}n”  
 brand_input_str += f”result: n”  
 brand_input_str += “---n”  
 brand_input_str += f”{serp_map[keyword]}n”  
 brand_input_str += “---nn”

serp : title, snippet of top 3 organic result content
