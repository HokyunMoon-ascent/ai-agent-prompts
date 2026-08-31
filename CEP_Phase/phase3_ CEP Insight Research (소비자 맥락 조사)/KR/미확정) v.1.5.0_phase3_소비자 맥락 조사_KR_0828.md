<!-- 미확정) v.1.5.0_phase3_소비자 맥락 조사_KR_0828.md (updated 2026-08-28) -->
<!-- v.1.0.0 기반 개정. 3단 분업(P3 원문 전달 → P4 CEP 변환 → P5.5 그라운딩)의 P3 몫.
     이 단계는 판정하지 않는다. 출처 화자 판정은 P5.5 v.1.0.3 이 맡는다.
     개정 3건: (1) 인용 verbatim 의무화 (2) 출처 정체를 지우지 말 것(화자·시제·홍보 문구 보존) (3) "chose to use or buy" 삭제.
     ⚠ 백엔드 선행 필요: search_context_size 를 'low' → 'high'. 스니펫만 오면 verbatim 을 만들 수 없다.
     출력 구조·입력 변수는 v.1.0.0 그대로 — 하류(P4)·검수기 호환을 위해 손대지 않았다. -->

## Phase 3 — CEP Insight Research (소비자 맥락 조사)

### 목적

소비자가 특정 제품을 **떠올리게 되는 실제 생활 맥락, 상황, 트리거**를 조사합니다.
CEP(Category Entry Point) 발견에 중점을 두며, 온라인 커뮤니티/리뷰/SNS를 활용합니다.

**이 단계는 해석도 판정도 하지 않습니다.** 찾은 글에 적힌 말을 그대로 옮겨 옵니다.

CEP 문장으로 다듬는 일은 Phase 4, 그 출처가 쓸 만한지 판정하는 일은 Phase 5.5의 몫입니다.
여기서 미리 요약하면 두 단계 모두 근거를 잃습니다. **특히 화자와 시제를 다듬으면 Phase 5.5가 판정 자체를 할 수 없게 됩니다.**

### v.1.0.0 대비 변경점

| #   | 무엇                                                           | 왜                                                                                                                                                                                                         |
| --- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | 번호 문단에 **verbatim 인용 의무화**                           | 불릿이 P3의 요약이면 검수는 "요약↔인용"만 대조하게 된다. 원문에 없는 내용도 통과한다(검수 pass 108장 중 64장이 정의 위반)                                                                                  |
| 2   | `# Preserve the Source's Fingerprints` 절 신설                 | **이 개정의 핵심.** 판정을 P5.5로 미루려면 판정할 재료가 하류까지 살아 있어야 한다. 3인칭을 1인칭으로 바꾸거나 협찬 고지를 빼고 옮기면, 판매 페이지가 소비자 글과 똑같이 보이게 되어 P5.5가 판정할 수 없다 |
| 3   | `# Where to Look` — 검색처 안내만, 판정 없음                   | 어디를 뒤질지는 알려주되 "이 페이지를 쓸지 말지"는 판정하지 않는다. 나노 모델에 해석을 시키지 않는 것이 3단 분업의 취지다                                                                                  |
| 4   | `explain when and why they chose to use or buy a product` 삭제 | 이미 산 사람의 글을 가져오라는 지시였다. 구매 이후 장면 29장의 출처                                                                                                                                        |
| 5   | 문단 끝 출처 링크 형식 명시 + 대시 목록 금지                   | 검수기는 번호 목록 항목만 인용 단위(`section_id` + `bullet_index`)로 인식한다. 도메인은 P5.5의 판정 근거이기도 하다                                                                                        |
| 6   | Tone 절 축소                                                   | "친근하게 다시 써라"가 verbatim 인용과 충돌했다                                                                                                                                                            |

> **화자 판정은 여기서 하지 않습니다.** 0828 실측에서 CEP 정의 위반 176장 중 146장이 "근거로 쓴 글의 화자가 소비자가 아님"이었지만,
> 그 판정은 P5.5 v.1.0.3이 맡습니다. 이 단계의 몫은 **판정할 수 있는 상태로 넘기는 것**입니다.

### 입력 변수

v.1.0.0과 동일합니다. **신규 변수 없음** — 백엔드 주입 코드를 고치지 않아도 됩니다.

| 변수                     | 설명                                        | 예시                   |
| ------------------------ | ------------------------------------------- | ---------------------- |
| `{{product_name}}`       | 제품명 또는 브랜드명                        | `갤럭시 S25 Ultra`     |
| `{{region}}`             | 타깃 시장                                   | `South Korea`          |
| `{{response_language}}`  | 응답 언어                                   | `Korean`               |
| `{{research_date}}`      | 조사 기준일                                 | `2026.08.28`           |
| `{{category}}`           | [선택] 제품 카테고리 (Product Anchors 결과) | `스마트폰, 플래그십폰` |
| `{{community_examples}}` | 국가별 커뮤니티 예시                        | (아래 참조)            |

#### 국가별 커뮤니티 예시 (`{{community_examples}}`)

v.1.0.0과 동일합니다.

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
각 섹션은 번호 + 인사이트 문장으로 구성. 섹션당 1~3개 번호 문단(출처가 뒷받침하는 만큼만).
**각 번호 문단에는 소비자 발화의 원문 인용이 최소 1개 들어갑니다.**

### 요청 모델 및 파라미터

| 파라미터          | 값                                                                                              |
| :---------------- | :---------------------------------------------------------------------------------------------- | ---- | ------------------------------------------ |
| model             | 'gpt-5.4-nano'                                                                                  |
| input             | prompt 문자열                                                                                   |
| text.format.type  | 'text'                                                                                          |
| text.verbosity    | 'low'                                                                                           |
| reasoning         | enableReasoningSummary가 true면 { effort: 'low', summary: 'concise' }, 아니면 { effort: 'low' } |
| tools             | [{ type: 'web_search', user_location: { type: 'approximate', country: 'KR'                      | 'JP' | 'US' }, **search_context_size: 'high'** }] |
| store             | false                                                                                           |
| include           | ['web_search_call.action.sources']                                                              |
| max_output_tokens | 128000                                                                                          |
| stream            | true                                                                                            |

> ⚠ **`search_context_size` 상향이 이 개정의 전제입니다.** `'low'`는 검색 스니펫만 돌려줍니다.
> 모델이 본문을 본 적이 없으면 원문 인용을 만들 수 없고, 만들라고 시키면 지어냅니다.
> 백엔드가 `'high'`로 올리기 전에는 이 프롬프트를 배포하지 마세요.

---

### Prompt 템플릿

```
<!-- 미확정) v.1.5.0_phase3_소비자 맥락 조사_KR_0828.md (updated 2026-08-28) -->

# Role
You are a consumer insight researcher specializing in Category Entry Point (CEP) discovery.
Your task is to find, and faithfully transcribe, what real consumers said about the situations that led them toward this product category.

# What This Step Does and Does Not Do
You DO: search, and copy down what people wrote — their words, their voice, their situation — along with what kind of page it came from.
You DO NOT: rewrite those words into polished insight sentences, decide what counts as a Category Entry Point, or decide whether a source is trustworthy enough to use.
Later steps do all of that. They never see the web page — they see only your text. Anything you summarize away, or tidy up, is gone for good.

# Task
Conduct web research to discover the real-life **situations, triggers, and contexts** that lead consumers in the target market toward this product category.
Search online communities, Q&A platforms, social media posts, and personal blogs where ordinary people describe their own circumstances in their own words.

## Community-Based Search (required, not optional)
For every search you run, append 1–2 major local community/platform names (relevant to the market/category) at the **END** of the query. Generic queries return mostly sales pages and articles, in which nobody describes their own life.
{{community_examples}}

# Where to Look

Search where consumers talk to each other, not where products are sold. Community threads, Q&A posts, and personal posts in which ordinary people describe their own circumstances in their own words are what this step is for. Generic queries return sales pages and articles, which contain no one's life.

**You are not the filter.** Do not decide whether a page "counts" as a valid entry point, and do not throw a page away because it looks commercial. A later step makes that call, and it can only make it from what you write down.

# Preserve the Source's Fingerprints — Do Not Launder It

This is the most important rule in this step. A later step has to judge **who wrote each page and why**. It can only see what you hand over. If you smooth a page into neutral prose, that judgment becomes impossible, and a sales page reads exactly like a consumer's post.

So when you transcribe:

- **Keep the voice as it is.** If the source speaks in first person ("I…", "저는…", "私は…"), keep the first person. If it speaks *about* consumers in third person ("many users find…", "고객들은…"), keep the third person. **Never convert third person into first person.** That single change is what makes an article look like a testimony.
- **Transcribe promotional and disclosure language when it is present** — sponsorship notices, affiliate disclaimers, discount codes, "link below", price and model listings. Do not quietly drop them because they are not about a life situation. They are the evidence of what kind of page this is.
- **Keep the tense and the purchase status.** If the writer says they already bought the thing and are describing life afterwards, write it that way. Do not restate a solved problem as if it were still unsolved.
- **Say what kind of page it is when the page itself makes that clear** — an interview, a press release, a buying guide, an institutional notice, a shop listing. One short phrase in your own sentence is enough.
- **Always end the paragraph with the source link** `([domain](url))`. The domain is part of the evidence.

Nothing here asks you to reject a page. It asks you not to disguise one.

# Research Focus
For each situation you find, capture the dimensions **that the source actually states**. Leave the rest out — do not fill gaps by inference.

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

Prefer concrete, specific situations over abstract generalizations
(❌ "people who exercise" → ✅ "morning runners who need quick hydration before 6am commute")
Focus on concrete, natural situations that could realistically occur in everyday life.

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
- Number sections continuously from 1 to the last section. **Never restart numbering.** A downstream checker locates quotations by section number; duplicate numbers make real quotations register as missing.

## Section Body — Quote, Don't Summarize
- Write **1 to 3 numbered paragraphs** per section (`1.` `2.` `3.`) — only as many as the sources support. Do not pad to three.
- **Every numbered paragraph MUST contain at least one verbatim quotation of a consumer's own words**, wrapped in quotation marks.
  - Copy the sentence exactly as it appears in the source: keep the original spelling, slang, abbreviations, typos, and sentence endings.
  - Do NOT paraphrase it, shorten it, translate it into neutral phrasing, or make it more articulate. The rough wording is the evidence.
  - If a page has no first-person sentence you can quote, that page does not become a paragraph.
- Around the quotation, add only what the source states — who is speaking, what they were doing, what they were missing. Do not add a time, place, feeling, or action the source does not state.
- **End every numbered paragraph with its source link**, formatted as `([domain](url))`.
- Each numbered paragraph must be self-contained and describe ONE distinct consumption context.
- Do NOT use dash (`-`) bullets to list quotations. Only numbered list items are recognized as citable units downstream; anything in a dash list is unreachable and will make cards built on it fail inspection.
- Do not write prose outside the numbered paragraphs. Start with `1.` on the line after the section heading.

## Tone
- Write EVERYTHING in **{{response_language}}**.
- Your own connecting sentences should be plain and easy to read.
- **This does not apply to the quotations.** Never clean up a consumer's wording to match your tone.
- Avoid marketing jargon in your own sentences (do not use terms like CEP, 7W Framework, Category Entry Point).

## Formatting Rules
- Section headings MUST use ## prefix with number and insight sentence
- End response immediately after the last section
- No summary, conclusion, or closing remarks

## Example (structure and quoting depth to match)
## 1. <Section title>
1. <One context, stated only as far as the source states it, containing a verbatim quote: "…". > ([domain](url))
2. <A second, distinct context, with its own verbatim quote: "…". > ([domain](url))

# Input
- Brand or Product: **"{{product_name}}"**
{{category_line}}- Target Market: **{{region}}**
```
