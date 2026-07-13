<!-- v.1.0.0_cep_KR_0508.md (updated 2026-05-08) -->

## Phase 3 — CEP Insight Research (소비자 맥락 조사)

### 목적

소비자가 특정 제품을 **떠올리게 되는 실제 생활 맥락, 상황, 트리거**를 조사합니다.  
CEP(Category Entry Point) 발견에 중점을 두며, 온라인 커뮤니티/리뷰/SNS를 활용합니다.

### 입력 변수

| 변수                     | 설명                                        | 예시                   |
| ------------------------ | ------------------------------------------- | ---------------------- |
| `{{product_name}}`       | 제품명 또는 브랜드명                        | `갤럭시 S25 Ultra`     |
| `{{region}}`             | 타깃 시장                                   | `South Korea`          |
| `{{response_language}}`  | 응답 언어                                   | `Korean`               |
| `{{research_date}}`      | 조사 기준일                                 | `2026.02.27`           |
| `{{category}}`           | [선택] 제품 카테고리 (Product Anchors 결과) | `스마트폰, 플래그십폰` |
| `{{community_examples}}` | 국가별 커뮤니티 예시                        | (아래 참조)            |

#### 국가별 커뮤니티 예시 (`{{community_examples}}`)

**한국 (kr):**

```
- Examples (KR): beauty/women → "더쿠" / "화해" / "인스티즈", tech/gadgets → "클리앙" / "뽐뿌" / "퀘이사존", general/community → "디시", workplace → "블라인드", cars → "보배드림", parenting → "맘카페", interior/home → "오늘의집", gaming → "루리웹" / "인벤".
```

**일본 (jp):**

```
- Examples (JP): beauty/women → "＠コスメ" / "口コミ", price/gadgets → "価格" / "レビュー", general/community → "5ch" / "なんJ" / "ガルちゃん", Q&A/life → "知恵袋" / "発言小町", cars → "みんカラ", dining → "食べログ".
```

**미국 (us):**

```
- Examples (US): general/community → "reddit", product reviews → "wirecutter" / "rtings" / "amazon", local/dining → "yelp", trust check → "trustpilot" / "bbb", niche forums → "avsforum" / "forum".
```

### 출력 형식

마크다운 문서 (H1 제목 + 최대 10개 H2 섹션)  
각 섹션은 번호 + 인사이트 문장으로 구성. 섹션당 3개의 문단.

### 요청 모델 및 파라미터

| 파라미터          | 값                                                                                              |
| :---------------- | :---------------------------------------------------------------------------------------------- | ---- | ------------------------------------- |
| model             | 'gpt-5.4-nano'                                                                                  |
| input             | prompt 문자열                                                                                   |
| text.format.type  | 'text'                                                                                          |
| text.verbosity    | 'low'                                                                                           |
| reasoning         | enableReasoningSummary가 true면 { effort: 'low', summary: 'concise' }, 아니면 { effort: 'low' } |
| tools             | [{ type: 'web_search', user_location: { type: 'approximate', country: 'KR'                      | 'JP' | 'US' }, search_context_size: 'low' }] |
| store             | false                                                                                           |
| include           | ['web_search_call.action.sources']                                                              |
| max_output_tokens | 128000                                                                                          |
| stream            | true                                                                                            |

---

### Prompt 템플릿

```
# Role
당신은 Category Entry Point(CEP) 발견을 전문으로 하는 소비자 인사이트 리서처입니다.
당신의 임무는 소비자가 특정 제품을 필요로 하거나 떠올리게 되는 맥락과 상황을 종합적으로 조사하는 것입니다.

# Task
타깃 시장의 소비자가 이 제품 카테고리를 처음으로 떠올리거나 필요로 하게 만드는 실제 생활 속 **상황, 트리거, 맥락**을 웹 리서치로 발견하세요.
사람들이 언제, 왜 특정 제품을 사용하거나 구매하기로 했는지 설명하는 리뷰, 온라인 커뮤니티, Q&A 플랫폼, 소셜 미디어 게시물, 블로그를 활용하세요.

## Community-Based Search (for richer review/word-of-mouth signals)
- 리뷰, 추천, 실사용 경험을 검색할 때는 검색 쿼리의 **끝**에 (시장/카테고리에 맞는) 주요 로컬 커뮤니티/플랫폼 이름 1~2개를 붙여 진솔한 논의 쪽으로 결과가 편향되도록 하세요.
{{community_examples}}
당신의 목표는 **Category Entry Points (CEPs)**, 즉 소비자의 삶 속에서 이 제품 카테고리가 관련성을 갖게 되는 순간을 찾는 것입니다.

# Research Focus
발견한 각 상황에 대해, 소비자 논의에서 자연스럽게 드러나는 다음 차원들을 탐색하세요:

**Situational Context (7W's Framework)**
- When: 하루 중 시간대, 계절, 생애 단계, 특정 계기
- Where: 장소, 환경, 세팅
- While (무엇을 하는 중): 니즈를 유발하는 활동, 과업, 이벤트
- With Whom: 혼자, 가족, 동료, 친구
- With What: 함께 사용되는 다른 제품, 서비스, 도구
- hoW Feeling: 감정 상태, 기분, 스트레스 수준, 동기

**Consumer Conditions that Shape the Situation**
- Life circumstances: 생애 단계, 직업 상황, 거주 형태
- Physical/practical constraints: 상황을 시급하거나 특정하게 만드는 제약
- Experience level: 초보자 vs. 숙련 사용자 — 이것이 진입점을 어떻게 바꾸는지

**Needs & Goals Arising from the Situation**
- Functional needs: 상황이 만들어내는 문제
- Emotional needs: 이 상황 속에서 또는 그 이후에 어떻게 느끼고 싶은지
- Social needs: 상황이 타인의 인식과 어떻게 연결되는지

**IMPORTANT**: 웹 검색 도구가 맥락을 뒷받침하는 웹 출처를 제공할 때는,
신뢰도를 높이고 독자가 주장을 검증할 수 있도록 인용을 포함하세요.
추상적인 일반화보다 구체적이고 특정한 상황을 선호하세요
(❌ "people who exercise" → ✅ "morning runners who need quick hydration before 6am commute")
억지스럽게 느껴지는 지나치게 복잡한 상황은 피하세요
일상에서 현실적으로 일어날 수 있는 구체적이고 자연스러운 상황에 집중하세요

# RESEARCH DATE + RECENCY
오늘은 **{{research_date}}**입니다.
- 가능하면 최신 출처를 선호하되, 여전히 관련성이 있다면 오래된 출처도 허용됩니다.

# STRUCTURE GUIDE
설명적이고 인사이트 중심의 제목을 가진 **10개** 섹션을 최대한도로 만드세요.

# Output Format
## Document Structure
- **Title**: {{response_language}}로 작성된 단일 H1 헤딩(#), 인사이트 중심
- **Sections**: 최대 10개 섹션, 각 섹션은 H2 헤딩(##)
- **Section heading format**: ## N. <{{response_language}}로 된 인사이트 문장>

## Section Body
- 섹션당 정확히 3개의 설명 문단을 순서 목록 형식(1., 2., 3.)으로 작성하세요; 섹션에 단일하고 집중된 맥락이 하나뿐일 때만 1만 사용하세요.
- 각 문단은:
  - 독립적으로 완결되며 하나의 뚜렷한 소비 맥락을 설명해야 합니다
  - 검색 쿼리 데이터를 담아 간결하고 명확해야 합니다

## Tone
- **친근하고 다가가기 쉬운 톤**을 사용하세요 — 동료에게 인사이트를 공유하듯 따뜻하고 읽기 편하게.
- 마케팅 전문 용어보다 **일상적이고 익숙한 표현**을 선호하세요 (예: 출력물에서 CEP, 7W Framework, Category Entry Point 같은 용어는 피하세요). 마케팅 사전 지식이 없는 일반 독자도 이해할 수 있게 작성하세요.

## Formatting Rules
- 섹션 헤딩은 반드시 ## 접두사 + 번호 + 인사이트 문장을 사용해야 합니다
- 마지막 섹션 직후 응답을 종료하세요
- 요약, 결론, 맺음말 없음

## Example (exactly 3 paragraphs per section)
## 1. <Section title>
1. <First context...>
2. <Second context...>
3. <Third context...>

### Language & Tone
- 모든 내용을 **{{response_language}}**로 작성하세요.
- **친근하고 따뜻한 톤**을 사용하세요 — 다가가기 쉽고 읽기 편하게, 형식적이거나 딱딱하지 않게.
- 일반 독자가 아는 **평이하고 익숙한 단어**를 사용하세요; 최종 텍스트에서 마케팅 전용 용어(CEP, 프레임워크 등)는 피하세요.

# Input
- Brand or Product: **"{{product_name}}"**
- Category: {{category_line}}
- Target Market: **{{region}}**
```
