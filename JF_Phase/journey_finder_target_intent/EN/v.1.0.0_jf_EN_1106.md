journey_finder_target_intent

**v. 25.11.06**

### Instructions

Analyze the given list of [Search Queries] and classify each query into a ‘Target Keyword’ (the word representing the object of purchase or use) and an ‘Intent Keyword’ (words indicating why this target was searched).

## Explanation of Target Keyword

A Target Keyword refers to a word that contains what the searcher wants to know, buy, eat, dreams of, wishes for, wants to get rid of, is concerned about, or is the object of desire. In other words, it is a word or phrase that can be called the core topic or subject of the search. Generally, a Target Keyword is the name of a specific product (brand keyword), the name of a brand (brand keyword), the category name of a service or product (non-brand keyword), a person's name, or a concept, and is usually a noun, pronoun, or nominal form.  
In most cases, it appears at the beginning of the search query, but in some cases, it may be located at the end. It is also positioned before the Intent Keyword, which contains the searcher's situation, context, or intent. The input [Seed Keyword] usually corresponds to the Target Keyword.

## Explanation of Intent Keyword

It refers to a word or phrase within the search query that pairs with the Target Keyword to clarify the searcher's context, situation, and intent. Intent Keywords are usually located after the Target Keyword, but in cases where adjectives or nouns acting as adjectives serve as the Intent Keyword, they may come before the Target Keyword. For example, in “32-inch monitor”, “monitor” is the Target Keyword, and “32-inch” shows interest in the size, thus acting as an adjective that reveals the searcher's intent, making it an Intent Keyword.

There are many standard words for Intent Keywords, with the following being representative examples:

1. Information Seeking: Used when wanting to know explanations, effects, causes, methods, etc., about the target, such as ~how to use, ~effects, ~side effects.
2. Problem Solving: Used for the purpose of resolving a specific problem, symptom, or error, such as ~problems, ~breakdown, ~error, ~issue.
3. Purchase Intent: Mainly used to confirm a purchase, such as ~price, ~where to buy, ~store, ~discount, ~used, ~place of purchase.
4. Indirect Experience Check: Used by buyers to check others' experiences, such as ~review, ~reviews, ~opinions, ~reddit, etc.
5. Informational: Used when purely wanting to check information, such as ~is, ~definition, ~meaning.
6. Comparative: When comparing two targets with "vs" → the Intent Keyword must include "vs" along with the second item (B) (e.g., "vs Galaxy"), ~comparison.

##Examples and Explanations for Classifying Target and Intent Keywords  
For the search term “recommended destinations for solo travel,” “destinations for solo travel” is the target keyword, and “recommended” is the intent keyword.  
For search terms like “popular trends,” “popular trends” is the target keyword, and there is no intent keyword.  
For “Galaxy S 25 price,” “Galaxy S 25” is the target keyword and “price” is the intent keyword.  
For “32-inch monitor,” ‘monitor’ is the target keyword, and “32-inch” is the intent keyword—it shows the searcher's interest in size, acting as an adjective that reveals their intent.  
For searches like “A vs B,” “A” is the target keyword, while ‘vs’ is a modifier indicating intent, making “vs B” the intent keyword.  
For “Starbucks Pumpkin Spice Latte,” the object of desire is “Starbucks Pumpkin Spice Latte.” Since the Starbucks brand itself isn't the desired object, the “Pumpkin Spice Latte” part must be included for it to be the desired object. Therefore, in this case, “Starbucks Pumpkin Spice Latte” is the target keyword, and there is no “intent keyword.”  
For “LG monitor review DC,” “LG monitor” is the target keyword, and “review DC” indicates the searcher's context, making it the intent keyword.  
For “skin flipping,” the entire phrase “skin flipping” is the target keyword.  
For “small pimples,” “small pimples” itself should be considered the target keyword the searcher wants to eliminate.

### Output Rules

For each search query, clearly distinguish and present the Target Keyword and Intent Keyword.  
If the Intent Keyword is not clear, you may provide only the Target Keyword.  
If the Target Keyword is not accurate, you may provide only the Intent Keyword.  
Do not provide additional explanations; return only the results in JSON format.

### Input

[Search Query]  
%(keywords)s

[Seed Keyword]  
%(keyword)s

### Output format

{  
 "results": [
 {"query": "vitamin c benefits", "target": "vitamin c", "intent": "benefits"},
 {"query": "retinol skin purging", "target": "retinol", "intent": "skin purging"},
 {"query": "vitamin c effect when", "target": "vitamin c", "intent": "effect"},
 {"query": "how to improve skin tone", "target": "skin tone", "intent": "how to improve"},
 {"query": "iPhone 15 preorder", "target": "iPhone 15", "intent": "preorder"},
 {"query": "Galaxy Watch 6 price comparison", "target": "Galaxy Watch 6", "intent": "price comparison"},
 {"query": "AirPods Pro 2nd gen review", "target": "AirPods Pro 2nd gen", "intent": "review"},
 {"query": "Nike Air Force 1 types", "target": "Nike Air Force 1", "intent": "types"},
 {"query": "Starbucks gift card how to use", "target": "Starbucks gift card", "intent": "how to use"},
 {"query": "Wayfair furniture discount", "target": "Wayfair furniture", "intent": "discount"},
 {"query": "how to call Uber", "target": "Uber", "intent": "how to call"},
 {"query": "YouTube Premium price increase", "target": "YouTube Premium", "intent": "price increase"},
 {"query": "Apple vs Samsung", "target": "Apple", "intent": "vs Samsung"},
 {"query": "32 inch monitor", "target": "monitor", "intent": "32 inch"},
 {"query": "Starbucks Pumpkin Spice Latte", "target": "Starbucks Pumpkin Spice Latte", "intent": ""},
 {"query": "solo travel destination recommendation", "target": "solo travel destination", "intent": "recommendation"},
 {"query": "LG monitor review reddit", "target": "LG monitor", "intent": "review reddit"},
 {"query": "chair good for back recommendation reddit", "target": "chair good for back recommendation", "intent": "reddit"},
 {"query": "long hours chair recommendation reddit", "target": "long hours chair recommendation", "intent": "reddit"},
 {"query": "study chair recommendation reddit", "target": "study chair recommendation", "intent": "reddit"},
 {"query": "Herman Miller store discount", "target": "Herman Miller store", "intent": "discount"},
 {"query": "used conference table", "target": "conference table", "intent": "used"},
 {"query": "used luxury chair", "target": "luxury chair", "intent": "used"},
 {"query": "Steelcase chair assembly reddit", "target": "Steelcase chair assembly", "intent": "reddit"},
 {"query": "Secretlab c30+ reddit", "target": "Secretlab c30+", "intent": "reddit"},
 {"query": "Herman Miller New Aeron how to use", "target": "Herman Miller New Aeron", "intent": "how to use"},
 {"query": "fake iPhone", "target": "iPhone", "intent": "fake"},
 {"query": "how to wake up well", "target": "sleep", "intent": "how to wake up well"},
 {"query": "exercise to wake up", "target": "sleep", "intent": "exercise to wake up"},
 {"query": "how to wake up without coffee", "target": "sleep", "intent": "how to wake up without coffee"},
 {"query": "how to wake up while studying", "target": "sleep while studying", "intent": "how to wake up"},
 {"query":": how to wake up after all nighter", "target": "sleep after all nighter", "intent": "how to wake up"},
 {"query": "Tylenol day after drinking", "target": "Tylenol", "intent": "day after drinking"},
 {"query": "Tylenol after coffee", "target": "Tylenol", "intent": "after coffee"}
 ]  
}
