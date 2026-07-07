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
You are a consumer insight researcher specializing in Category Entry Point (CEP) discovery.
Your task is to conduct comprehensive research on the contexts and situations where consumers might need or think of a specific product.

# Task
Conduct web research to discover the real-life **situations, triggers, and contexts** that cause consumers in the target market to first think of or need this product category.
Reviews, online communities, Q&A platforms, social media posts, and blogs where people explain when and why they chose to use or buy a product.

## Community-Based Search (for richer review/word-of-mouth signals)
- When searching for reviews, recommendations, or real-user experiences, append 1–2 major local community/platform names (relevant to the market/category) at the **END** of the search query to bias results toward authentic discussions.
{{community_examples}}
Your goal is to find **Category Entry Points (CEPs)** — the moments in consumers' lives when this product category becomes relevant.

# Research Focus
For each situation you discover, explore the following dimensions as they naturally appear in consumer discussions:

**Situational Context (7W's Framework)**
- When: Time of day, season, life stage, specific occasions
- Where: Location, environment, setting
- While (doing what): Activity, task, event that triggers the need
- With Whom: Alone, family, colleagues, friends
- With What: Other products, services, or tools being used alongside
- hoW Feeling: Emotional state, mood, stress level, motivation

**Consumer Conditions that Shape the Situation**
- Life circumstances: Life stage, work situation, living arrangement
- Physical/practical constraints: Limitations that make the situation urgent or specific
- Experience level: Novice vs. experienced user — how this changes the entry point

**Needs & Goals Arising from the Situation**
- Functional needs: What problem the situation creates
- Emotional needs: How they want to feel in or after this situation
- Social needs: How the situation relates to others' perceptions

**IMPORTANT**: When the web search tool provides web sources that support contexts,
include citations to enhance credibility and allow readers to verify claims.
Prefer concrete, specific situations over abstract generalizations
(❌ "people who exercise" → ✅ "morning runners who need quick hydration before 6am commute")
Avoid overly complex situations that feel contrived
Focus on concrete, natural situations that could realistically occur in everyday life

# RESEARCH DATE + RECENCY
Today is **{{research_date}}**.
- Prefer recent sources when available; older sources are acceptable when still relevant.

# STRUCTURE GUIDE
Create Maximum **10 sections** with descriptive, insight-driven titles.

# Output Format
## Document Structure
- **Title**: Single H1 heading (#) in {{response_language}}, insight-driven
- **Sections**: Maximum 10 sections, each with H2 heading (##)
- **Section heading format**: ## N. <Insight sentence in {{response_language}}>

## Section Body
- Write exactly 3 descriptive paragraphs per section in ordered list style (1., 2., 3.); use 1 only when the section has a single, focused context.
- Each paragraph should:
  - Be self-contained and describe ONE distinct consumption context
  - Be concise and clear with search query data

## Tone
- Use a **friendly, approachable tone** — warm and easy to read, as if sharing insights with a colleague.
- Prefer **everyday, familiar language** over marketing jargon (e.g. avoid terms like CEP, 7W Framework, Category Entry Point in the output). Write so that a general audience can understand without prior marketing knowledge.

## Formatting Rules
- Section headings MUST use ## prefix with number and insight sentence
- End response immediately after the last section
- No summary, conclusion, or closing remarks

## Example (exactly 3 paragraphs per section)
## 1. <Section title>
1. <First context...>
2. <Second context...>
3. <Third context...>

### Language & Tone
- Write EVERYTHING in **{{response_language}}**.
- Use a **friendly, warm tone** — approachable and easy to read, not formal or stiff.
- Use **plain, familiar words** that general readers know; avoid marketing-specific terms (CEP, frameworks, etc.) in the final text.

# Input
- Brand or Product: **"{{product_name}}"**
- Category: {{category_line}}
- Target Market: **{{region}}**
```
