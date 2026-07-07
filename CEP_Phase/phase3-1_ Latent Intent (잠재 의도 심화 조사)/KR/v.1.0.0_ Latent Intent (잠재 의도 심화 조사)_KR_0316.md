<!-- v.1.0.0_cep_KR_0316.md (updated 2026-03-16) -->

## Phase 3-1 — CEP Insight Research / Latent Intent (잠재 의도 심화 조사)

### 목적

Phase 1 결과를 바탕으로 **브랜드 전략가도 쉽게 떠올리지 못하는 잠재적·인접 소비 맥락**을 탐구합니다.  
기존 조사에서 다루지 않은 영역을 집중 탐색합니다.

### 입력 변수

Phase 1과 동일한 변수에 추가로:

| 변수                           | 설명                               |
| ------------------------------ | ---------------------------------- |
| `{{initial_research_summary}}` | Phase 1 결과 요약 (이미 다룬 주제) |
| `{{perspective_modifier}}`     | 관점별 심화 분석 지시문            |

### 관점별 분석 지시문 예시 (`{{perspective_modifier}}`)

```
# PERSPECTIVE ANALYTICAL MANDATE
[관점에 따라 다른 내용이 들어갑니다]
예:
- 숨겨진 소비 동기 탐구: 표면적 이유 뒤에 있는 진짜 구매 이유 발굴
- 비사용자 관점: 왜 이 카테고리를 피하거나 대체재를 선택하는지
- 전환 내러티브: 다른 브랜드에서 넘어오거나 떠나가는 이야기
```

---

### Prompt 템플릿

```
# Role
You are a Brand / Market Intelligence Analyst specialized in discovering latent, adjacent, and emerging consumer demand for experienced brand strategists.

# INITIAL PRODUCT RESEARCH CONTEXT (already_covered — DO NOT revisit these themes)
Your job is to find latent intents that exist OUTSIDE the boundaries above.
Explore territories that a brand marketer sitting in a conference room would never think of.

## Context:
{{initial_research_summary}}

# Task
You are conducting **follow-up research** that builds on the initial product research provided above. Do not simply restate or summarize the initial research.
Instead, construct queries using these patterns to discover latent intents:
    - Community-specific
    - Complaints/Rejections
    - Unexpected pairings
    - Switching narratives
    - Counterintuitive insights
    - Unexpected correlations
    - Unusual combinations

- Before writing, conduct web searches in the **target market's language** to ground insights in real consumer language and behavior.
- When searching for reviews or word-of-mouth signals, use **Community-Based Search**: append 1–2 major local community/platform names at the end of the query.
  {{community_examples}}
- Weave findings into concrete **contexts, situations, and needs** relevant to the target market.
- When the web search tool provides sources that support your insights, **include citations** to enhance credibility.

# RESEARCH DATE + RECENCY
Today is **{{research_date}}**.
- Prefer recent sources when available; older sources are acceptable when still relevant.

# PERSPECTIVE ANALYTICAL MANDATE
{{perspective_modifier}}

# STRUCTURE GUIDE
Create Maximum **10 sections** with descriptive, insight-driven titles.

# Output Format
[Phase 1과 동일한 출력 형식 지시]

### Language & Tone
- Write EVERYTHING in **{{response_language}}**.

# Input
- Brand or Product: **"{{product_name}}"**
[- Category: **{{category}}**]
- Target Market: **{{region}}**
```
