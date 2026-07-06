### Instruction

The following describes each stage of the Consumer Decision Journey.  
You will be given two inputs: a list of [intent keywords] and a list of original [keywords].  
Your task is to judge the groups (grouped by intent keywords) holistically to determine which consumer decision journey stage each group belongs to, and then return the number for that stage. Each keyword group (i.e., a group clustered by a single intent keyword) will have one number.  
“Intent keywords” indicate the searcher’s (consumer’s) situation, context, or reason for searching, and they are crucial for identifying which stage of the journey the consumer is currently in.  
Return only the stage number — no additional explanations.

[Consumer Decision Journey Stages]

1. Initial Exploration:

- This is the stage where consumers search for information to understand which brand, product, or service would be best for them to purchase. At this point, they often have no specific knowledge or familiarity with particular brands or products, so their searches tend to use broad or vague keywords.
- In this stage, consumers focus on the problem, task, or need they are trying to solve or fulfill. They may discover a product or service category for the first time, or realize their need or desire for that type of product or service.

2. Own & Service:

- After purchasing, consumers search for information or services to solve problems or learn how to use the product.
- This includes product usage, troubleshooting, repairs, community discussions, or repeat purchases.
- Common examples of intent keywords in this stage include: “how to use”, “guide”, “troubleshooting”, “repair”, “customer service”, “broken”, “defective”, “community”, “user group”, “customer training”, “repurchase”etc

3. Other:

- Keywords that are not related to purchase intent.
- Example intents: “career”, “jobs”, “stock”, “share price”, “dividend”, etc.

### Input

[Intent Keywords]  
%(intents)s

[Keywords]  
%(keywords)s

**### Output Format**  
**{{**  
 **"Initial_Exploration": [keywords],**  
 **"Own_Service": [keywords],**  
 **"Other": [keywords]**  
**}}**

**### Input**  
**- Keywords:**  
**%(keywords)s**
