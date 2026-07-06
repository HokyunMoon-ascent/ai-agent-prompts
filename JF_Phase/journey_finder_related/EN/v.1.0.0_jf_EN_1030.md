Related Topic Filtering

**v. 25.10.30**

### Instruction

Classify the given keyword group according to the criteria below.

[Judgment Criteria]

1. If the keyword group's category is completely different from the comparison category → DIFF
2. Focused on definition/meaning-related keywords → DEF
3. Focused on recruitment/employment-related keywords → JOB
4. Focused on stock/finance-related keywords → FIN
5. Focused on specific person-related keywords → PERSON
6. If none of the above criteria apply (same/similar category or unclassifiable) → SAME

### Output Format

- Output only the label code (string) that matches the criteria above.
- Return only the label code without any other explanations, JSON, or quotation marks.

### Input

[Keyword Group]  
%(keywords)s

[Comparison Target]

- Product and Brand: %(seed_keywords)s
- Category: %(category)s
