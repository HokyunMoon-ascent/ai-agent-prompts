journey_finder_target_intent

**v. 25.10.30**

### Instructions

Analyze the provided list of search queries and classify each query into 'Target Keyword' and 'Intent Keyword'.

**Target Keyword:**  
The core word or phrase that represents the main subject of the search.  
This usually refers to a specific product, service, brand, person, or concept.  
The target keyword is usually placed before the intent keyword.  
For comparison queries using "vs" (e.g., "product1 vs product2"), the target keyword should be the first product or item before "vs".

**Intent Keyword:**  
The word or phrase indicating the user's purpose or reason for searching for a specific target keyword. These can appear in various forms:

Information Seeking: Desiring explanations, causes, effects, or methods related to the target (e.g., 사용법, 효과, 부작용).  
Problem Solving: Looking for solutions or information regarding a specific issue or situation (e.g., 피부 뒤집어짐, 오류 해결).  
Purchase Intent: Seeking information related to buying a product or service (e.g., 가격, 추천, 비교, 구매처).  
Information Source: Aiming to obtain information from a specific platform or community (e.g., 더쿠, 디시).  
Comparison: When comparing products or items using "vs", the intent keyword should include "vs" and the second item (e.g., "vs 갤럭시").  
When multiple words are used together to represent a single intent, should recognize them as a single intent keyword (e.g., 피부 뒤집어짐, 좁쌀 여드름, 디시 후기).

Clearly distinguish the target keyword and intent keyword for each search query.  
If the intent keyword is not explicitly evident, you may provide only the target keyword.

Just return the result without any additional explanation.

### Example

{{
  "results": [
    {{"query": "비타민c 효능", "target": "비타민c", "intent": "효능"}},  
 {{"query": "레티놀 피부 뒤집어짐", "target": "레티놀", "intent": "피부 뒤집어짐"}},  
 {{"query": "비타민c 효과 언제부터", "target": "비타민c", "intent": "효과"}},  
 {{"query": "피부 톤 개선 방법", "target": "피부 톤", "intent": "개선 방법"}},  
 {{"query": "아이폰 15 사전예약", "target": "아이폰 15", "intent": "사전예약"}},  
 {{"query": "갤럭시 워치 6 가격 비교", "target": "갤럭시 워치 6", "intent": "가격 비교"}},  
 {{"query": "에어팟 프로 2세대 후기", "target": "에어팟 프로 2세대", "intent": "후기"}},  
 {{"query": "나이키 에어 포스 1 종류", "target": "나이키 에어 포스 1", "intent": "종류"}},  
 {{"query": "스타벅스 기프티콘 사용법", "target": "스타벅스 기프티콘", "intent": "사용법"}},  
 {{"query": "오늘의집 가구 할인", "target": "오늘의집 가구", "intent": "할인"}},  
 {{"query": "카카오택시 호출 방법", "target": "카카오택시", "intent": "호출 방법"}},  
 {{"query": "유튜브 프리미엄 가격 인상", "target": "유튜브 프리미엄", "intent": "가격 인상"}},  
 {{"query": "애플 vs 삼성", "target": "애플", "intent": "vs 삼성"}}  
 ]  
}}

### Input

%(keywords)s
