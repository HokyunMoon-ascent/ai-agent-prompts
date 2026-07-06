### Instruction

The following describes each stage of the Consumer Decision Journey.  
You will be given two inputs: a list of [intent keywords] and a list of original [keywords].  
Your task is **to determine, holistically for the entire group, which single decision journey stage number it belongs to.**  
“Intent keywords” indicate the searcher’s (consumer’s) situation, context, or reason for searching, and they are crucial for identifying which stage of the journey the consumer is currently in.  
Return only the stage number — no additional explanations.

[Consumer Decision Journey Stages]

1. Browsing:

- The consumer defines a specific category or solution to solve a problem and gathers objective information such as product specs, features, and prices of candidate brands.
- Consumers may also use queries made up only of product keywords (without explicit intent terms) when they’re simply curious about the product itself.
- Common examples of intent keywords in this stage include:  
  “features”, “specs”, “performance”, “weight”, “color”, “size”, “design”, “types”, “characteristics”, “lineup”, “[brand/product name] + meaning or definition”, etc.

2. Experience:

- The consumer explores subjective experiences and evaluations of candidate brands or products to make a purchase decision.
- Searches often focus on experience-based evidence such as user reviews, expert opinions, or online community feedback.
- Typical intent keyword examples in this stage:
  - Case1) Comparing multiple products: “vs”, “ranking”, “comparison”, etc.
  - Case2) Looking for reviews or recommendations: “review,” “recommendation”, “pros and cons”, “top rated”, “best-selling”, etc.
  - Case3) Includes site names where reviews are found: “Reddit”, “Wirecutter”, “CNET” etc.

3. Confirmation:

- This is the stage right before purchase, where the consumer verifies that their choice is the best and prepares for the actual transaction.
- The consumer performs a final check and proceeds to the actual purchase in order to buy the desired product under the best possible conditions.
- They search for information about when, where, and under what terms to buy — including coupons, shipping, promotions, events, or rental options.
- Common intent keywords in this stage include:  
  “buy”, “purchase”, “price”, “discount”, “deal”, “pre-order”, “release”, “used”, “event”, “where to buy”, etc.

4. Own & Service:

- After purchasing, consumers search for information or services to solve problems or learn how to use the product.
- This includes product usage, troubleshooting, repairs, community discussions, or repeat purchases.
- Common examples of intent keywords in this stage include: “how to use”, “guide”, “troubleshooting”, “repair”, “customer service”, “broken”, “defective”, “community”, “user group”, “customer training”, “repurchase”etc

5. Other:

- Keywords that are not related to purchase intent.
- Example intents: “career”, “jobs”, “stock”, “share price”, “dividend”, etc.

### Input

[Intent Keywords]  
%(intents)s

[Keywords]  
%(keywords)s
