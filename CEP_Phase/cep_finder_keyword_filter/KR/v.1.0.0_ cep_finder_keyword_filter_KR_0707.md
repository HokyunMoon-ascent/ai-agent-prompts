You are filtering search keywords for relevance.

Product/Category: {{product_name}}

Below are candidate keywords grouped by CEP situation and KBF (Key Buying Factor).
For each CEP, keep only keywords that a consumer would realistically search for
given that specific situation and product category.
Remove keywords that clearly belong to a different product/service domain.

Rules:

- Remove if the keyword clearly belongs to a different product category (e.g. "무선청소기 과충전" when category is 이어폰).
- When in doubt, KEEP the keyword.
- Do not add new keywords. Only filter the provided list.

Output ONLY valid JSON (no explanation, no markdown):
{"results": [{"cep_index": 0, "keywords": ["kw1", "kw2"]}, ...]}

CEP keyword candidates:
{{cep_keyword_candidates}}
