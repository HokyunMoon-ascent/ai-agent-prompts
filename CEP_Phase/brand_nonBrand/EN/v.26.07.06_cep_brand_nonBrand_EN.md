brand_nonBrand (CEP)

**v. 26.07.06**

### Instruction

Look at the given search query {{keyword}} and determine whether this query is a "brand query."  
A **"brand query"** refers to a query directly associated with a **specific brand** or a **specific product/model**.  
**Even if the word "brand" is included, if it is not referring to a specific brand but is used as a common noun (e.g., combined with ranking, recommendation, type, etc.), judge it as a "non-brand query."**

If the given query is a "brand query," return true for "brand"; if it is a "non-brand query," return false.  
If the query is a "brand query," **extract** the **part of the query** that serves as the basis for the judgment and return it in "brand_part" as well. (**Return it exactly as it appears in the query.**)  
If the query is a "non-brand query," return an empty string for "brand_part."

Return only the result, with no additional explanation.

**Brand query:**

- Company name (e.g., LG, 삼성)
- Service name (e.g., 유튜브, 넷플릭스)
- Brand name (e.g., 갤럭시, 그램, 아이폰)
- A specific brand's major **product (model) name** (e.g., s25, 프로 16)
- A query combining a brand name and a product category (e.g., 시디즈 게이밍 의자)

**Non-brand query:**

- Generic product category (e.g., 스마트폰, 노트북, 세탁기, 냉장고, 세정제, 화장품)
- Common noun (e.g., 레티놀, 사과)
- **A generic keyword that includes the word "brand" (e.g., 골프 브랜드 순위, 2025 최고의 브랜드)**

**Judgment criteria:**

- Judge solely based on whether the **query itself** contains a specific brand name, product name, or model name. (Since no separate search results are provided, judge only from the query text.)
- When a common noun and a brand name overlap and the meaning is ambiguous—such as '애플' (both a fruit and a brand) or '토스' (both a verb and a money-transfer app)—judge based on the commonly used meaning and the context of the query.

### Output Format

{
"keyword": str -> copy the input {{keyword}} value as-is,
"brand": bool,
"brand_part": str
}

### Input

{{keyword}}
