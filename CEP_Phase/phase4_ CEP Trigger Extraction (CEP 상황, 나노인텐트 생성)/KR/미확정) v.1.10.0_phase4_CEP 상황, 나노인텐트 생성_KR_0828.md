<!-- 미확정) v.1.10.0_phase4_CEP 상황, 나노인텐트 생성_KR_0828.md (updated 2026-08-28) -->
<!-- v.1.9.0 기반 개정. 문장 형태만 고칩니다 — 그라운딩 규칙은 v.1.9.0 그대로입니다.
     P5.5 v.1.0.4 와 짝입니다. 반드시 함께 배포하세요.
     P4만 올리면 뒷절을 써도 5.5 v.1.0.3 원칙 3이 도로 잘라냅니다.

     왜 고치는가 — 0828 선호 평가:
       v.1.9.0 산출물(릴리즈 D)은 출처 대조 기준으로 가장 정직한 회차인데
       4인 블라인드에서 현행 A에 밀렸습니다(A 32 · C 17 · D 10).
       측정해 보니 밀린 축이 정직성이 아니라 문장 설계였습니다.
         절 경계 수  A 1.35 → D 0.60      평균 글자수  A 60.6 → D 34.8
         연결절      A 45%  → D 18%       카테고리어   A 25%  → D 7%
       원인은 v.1.9.0 의 두 줄이었습니다. 둘 다 취지는 옳았고, 적용 범위가 넓었습니다.
         ① 게이트 3 "End the sentence at the cause, not at the shopping"
         ② "a single concrete moment" + "Do NOT name the category"
       "쇼핑으로 끝내지 마라"가 "카테고리와 연결하지 마라"까지 가서, 장면의 뒷절이 통째로 사라졌습니다.

     개정 3건: (1) 게이트 3 을 "쇼핑 금지"로 좁히고 연결절을 복원
               (2) Writing the Situation Sentence 를 형태·길이·문체·카테고리·그라운딩 5절로 재작성
               (3) Diversity Constraint 에 "첫 절이 같으면 한 장" 추가

     ⚠ 문장을 길게 만들면 지어내고 싶어집니다. 릴리즈 C 의 '날조된 디테일' 이 그 형태였습니다.
       안전장치는 (2)의 Grounding 절 마지막 줄입니다 — 뒷절은 사실 추가가 아니라 카테고리 연결입니다.
       배포 후 반드시 출처 소비자유형 % 를 함께 보세요. 그게 내려가면 A 로 되돌아간 것입니다.

     ⚠ v.1.9.0 이월 사항 그대로: 배열 길이 검증이 '정확히 N'이면 'N 이하'로 완화 필요.
       나노인텐트 내부 모순(머리 주석은 "제거", 템플릿·번들에는 살아 있음)도 미해결 이월. -->

## Phase 4 — CEP Trigger Extraction (CEP 상황 생성)

### 목적

Product Research 결과에서 **구체적인 CEP(Category Entry Point) 상황**을 추출합니다.
모든 상황은 리서치 본문에 실재하는 근거로 뒷받침되어야 하며, 근거 없는 디테일 생성을 금지합니다.

**이 단계가 해석을 전담합니다.** Phase 3은 소비자의 말을 그대로 옮겨 오기만 합니다.
그 원문을 CEP 문장으로 바꾸는 일, 그리고 그것이 진입 장면인지 판정하는 일이 여기 몫입니다.

**바꾸는 일에는 문장을 짓는 일이 포함됩니다.** 화자의 말을 그대로 옮겨 놓은 것은 변환이 아닙니다.

### v.1.9.0 대비 변경점

| #   | 무엇                                      | 왜                                                                                                                                                                       |
| --- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | 게이트 3 을 "쇼핑 금지"로 좁힘            | `End the sentence at the cause` 가 뒷절 금지로 읽혔다. A 의 어미 분포(`이 필요할 때` 8 · `하려고 할 때` 8 · `떠올리는 순간` 4)는 대부분 **필요**이지 비교·조사가 아니다 |
| 2   | `Writing the Situation Sentence` 재작성   | `a single concrete moment` → 평균 34.8자 한 절. `Do NOT name the category` → 카테고리어 7%. 연결절을 쓸 수단 자체가 막혀 있었다                                          |
| 3   | 문체를 표준 문어로 명시                   | `the consumer's everyday words` 가 원문 오탈자까지 실어 왔다(`새내기 여덬인데`, `습한집에서`, `뗄레야`)                                                                  |
| 4   | Diversity 에 "첫 절이 같으면 한 장" 추가  | 릴리즈 D 버티컬마우스 8장 중 4장이 같은 손목 통증이었다. 근사 중복쌍 0.7%(A·B·C 는 0)                                                                                   |

### 핵심 개념

- **CEP (Category Entry Point)**: 소비자가 특정 제품 카테고리를 필요로 하거나 떠올리게 되는 구체적 상황/트리거
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why — 다양성 점검용 관찰 렌즈 (강제 충족 쿼터 아님)
- **구체어 대응 원칙**: 상황에 등장하는 모든 시간·장소·발화·행동 표현은 제품 리서치 본문에 실재해야 함
- **두 절 원칙**: 카드 문장은 `[정황], [그래서 무엇이 필요해지는가] + 때`. 앞절만 있으면 불평이지 진입점이 아님
- **4대 환각 유형 금지**: ① 시간 단정(Time assertion) ② 없는 장소(Invented place) ③ 가짜 인용(Fake quotation) ④ 없는 행동(Invented action)

### 입력 변수

v.1.9.0·v.1.0.0과 동일합니다. **신규 변수 없음.**

| 변수                            | 설명                               | 예시               |
| ------------------------------- | ---------------------------------- | ------------------ |
| `{{product_name}}`              | 제품명                             | `탈모 샴푸`        |
| `{{country}}`                   | 국가 코드                          | `kr`               |
| `{{product_research_sections}}` | CEP Insight Research 결과 섹션들   | (마크다운 텍스트)  |
| `{{requested_count}}`           | 추출할 CEP 상황 개수               | `10`               |
| `{{category}}`                  | [선택] 제품 카테고리               | `샴푸, 헤어케어`   |
| `{{existing_situations}}`       | [선택] 기존 CEP 목록 (중복 방지용) | (이미 생성된 목록) |

> 주입 자리(`{{task_section}}` · `{{coverage_section}}` · `{{evidence_section}}`)는 v.1.0.0 위치 그대로 두었습니다.
> 지우면 백엔드가 넣는 문구가 사라집니다. 다만 그 문구가 본 개정 방향과 어긋나지 않는지는 **별도 확인이 필요합니다**
> (v.1.0.0 머리 주석이 이미 같은 확인을 요청해 두었고, 아직 처리되지 않았습니다).

### 출력 형식

JSON 배열 (최대 `{{requested_count}}`개). 스키마는 v.1.0.0과 동일합니다.

```json
[
  {
    "situation": "아침마다 샤워하고 나면 배수구에 머리카락이 한 움큼씩 빠져 있어서, 덜 빠지게 해줄 샴푸가 필요할 때",
    "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"],
    "evidence": "샤워하고 나면 배수구에 머리카락이 한 움큼씩 빠져 있어요",
    "section_refs": [2]
  }
]
```

> v.1.9.0 예시(`아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때`)는 앞절만 있는 문장이었습니다.
> 예시가 곧 규격으로 읽히므로 두 절 형태로 바꿨습니다.

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                              | 비고                                                             |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------------------------------------- |
| model             | **상향 모델 (백엔드 확인 필요)**                                    | 3단 분업의 전제 — 해석을 이 단계에 몰아넣는 설계입니다           |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | 단일 user 메시지 + input_text 파트                               |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                                                  |
| reasoning         | **{ effort: 'low' } 이상 (백엔드 확인 필요)**                       | v.1.0.0은 `'none'`. 판정 게이트 4개를 돌리려면 상향이 필요합니다 |
| tools             | []                                                                  |                                                                  |
| store             | false                                                               |                                                                  |
| include           | []                                                                  |                                                                  |
| max_output_tokens | 32768                                                               |                                                                  |

> ⚠ **모델명을 이 문서에서 확정하지 않았습니다.** v.1.9.0과 동일하게 비워 둡니다. 과금 기록에서 확인한 뒤 채워 넣으세요.

---

### Prompt 템플릿

````
<!-- 미확정) v.1.10.0_phase4_CEP 상황, 나노인텐트 생성_KR_0828.md (updated 2026-08-28) -->
# Role
You are a consumer behavior analyst specializing in Category Entry Point (CEP) identification.
Your expertise is in uncovering the real-life situations, triggers, and contexts that lead consumers to think of or need a specific product category.

{{category_section}}
# Product Research Results
{{product_research_sections}}

# How to Read the Research Above
The research is a set of numbered sections. Each section contains numbered paragraphs, and each paragraph carries at least one quotation of a consumer's own words plus a source link.
Those quotations are your only raw material. The connecting sentences around them are the researcher's, not the consumer's — do not treat them as facts about anyone's life.

# CEP Definition

CEP (Category Entry Point): the concrete life situation that makes a consumer think of this product category — the doorway through which someone who was **outside** the category walks in.
Consumers do not buy a product as an end in itself. They enter a category to solve a problem or to fit a situation, and that situation is the CEP.

A situation qualifies only if **all four** hold. Check them in order and drop the situation at the first failure.

1. **Entry, not usage.** The person does not already own or use this category. Someone optimizing, maintaining, replacing, or complaining about a product they already have is already inside.
   Being stuck with something *else* still counts as entry — doing the task by hand, borrowing, or making do with an adjacent product.
2. **Before the purchase.** The scene is the problem that sends them looking, not the satisfaction, regret, or comparison that follows a purchase.
3. **A life scene, ending in a need — not in shopping.** Something is happening in their life, and the sentence ends by naming what that circumstance makes them need.
   The need is named in the consumer's own everyday terms — "밀가루가 아닌 면", "손목에 부담이 덜한 마우스", "택시로 다닐 수 있는 풍경 좋은 곳".
   What must NOT appear is the shopping that follows entry: comparing, researching, reading reviews, weighing models, wondering whether to buy.
   Naming the need is the doorway. Naming the shopping is already inside.
4. **Recurring, not a one-off.** The situation comes back in ordinary life. A single accident, an injury, a move, a graduation happens once and passes — it is not an entry point.

Examples of what fails:
- ✗ "설치한 카메라로도 안 보이는 자리가 남을 때" — already owns it (fails 1)
- ✗ "후기를 비교하며 어떤 모델이 나은지 따질 때" — shopping, not a life scene (fails 2, 3)
- ✗ "손목이 아파서 덜 부담되는 입력 방식을 알아볼 때" — ends in researching (fails 3)
- ✗ "이사를 막 마쳤을 때" — happens once (fails 4)
- ✓ "집에 혼자 남은 강아지가 잘 있는지 확인할 방법이 없어서, 나가 있는 동안 안을 볼 수 있는 것이 필요할 때"

Note the difference between the third and the fifth. Both begin with wrist pain or worry. The failing one ends at the act of looking into options; the passing one ends at what the person needs. Keep the need, drop the looking.

# Task

{{task_section}}

# Writing the Situation Sentence

This is the one place where you transform rather than copy. Copying the consumer's sentence across unchanged is not a transformation.

**Shape.** One sentence in two clauses: `[the circumstance the consumer described], [what it makes them need] + 때`.
The first clause carries the concrete circumstance — when, who with, what constraint, what is blocking them.
The second clause names what they now need.
**A sentence with only the first clause is a complaint, not an entry point.** "혼자 밥 먹고 치우기 귀찮을 때" says nothing about what the person is about to reach for.

**Length.** Aim for 40–60 Korean characters. Under 25 the scene is too thin to picture; over 70 it stops being one moment.

**Register.** Write standard written Korean.
- Fix the source's typos, spacing, and colloquial contractions: `여덬` → `여자`, `습한집` → `습한 집`, `뗄레야` → `떼려야`, `땡기다` → `먹고 싶다`, `빵 터지다` → `웃기다`, `귀차니즘` → `만사가 귀찮음`.
- **Keep the consumer's concrete nouns.** "회사에서 준 납작한 마우스", not "업무용 장비". Words like "장비", "도구", "용품", "제품", "솔루션" can mean anything, so they mean nothing.
- The noun stays; the spelling does not. Normalizing the register must not blur what the person was actually talking about.

**Category.** Do NOT name a brand, a model, or a feature.
The category itself may appear **in the second clause only**, and only as the thing the person needs, written the way a consumer would say it — "밀가루가 아닌 면", "택시로 다닐 수 있는 풍경 좋은 곳". Never as a product label ("휴대용 물티슈가 필요할 때").

**Grounding.** Every concrete element in the **first clause** — time, place, action, spoken line, feeling — must trace back to the evidence quotation you cite.
The second clause names the need this card is an entry point for. **It adds no new fact about the person's life.** If you find yourself inventing a circumstance to make the second clause work, the card is not grounded — drop it.

# Evidence — Every Card Must Be Traceable

- `evidence`: copy the consumer's quotation **verbatim** from the numbered paragraph you used. Keep the original wording, spelling, and sentence ending. Do not paraphrase, shorten, or polish it. This string is matched against the research text downstream; any rewording makes a real citation register as unfounded.
  (The register rule above applies to `situation` only. `evidence` is never normalized.)
- `section_refs`: the section numbers from the Product Research Results that directly support this situation. Include 1–3 section numbers.
- **`section_refs` must never be empty and `evidence` must never be blank.** If you cannot point to a paragraph that supports a situation, that situation does not become a card — drop it.

## Four hallucination types — forbidden
1. **Time assertion**: do not state a time the quotation does not state ("in the first week", "around 2pm").
2. **Invented place**: do not add a location the quotation does not name.
3. **Fake quotation**: do not wrap your own paraphrase in quotation marks as if someone said it.
4. **Invented action**: do not add an action or a realization the quotation does not describe.

Two more constraints of the same kind:
- Do not combine facts from two different paragraphs into a claim that neither paragraph makes.
- Do not state something more strongly than the source does — no superlatives, no asserted causation where the source only suggests it.

# Prioritize Natural, Real-World Situations

- **Write situations that ordinary people would actually think of in their daily lives**
- Avoid forced or overly complex situations that feel contrived
- Focus on concrete, natural situations that could realistically occur in everyday life
- Think about what real consumers would actually search for or think about

# Diversity Constraint — Each Situation Must Be Independent

- **Each situation MUST represent a distinctly different persona, context, or life moment.**
- Do NOT generate situations that are merely rephrased versions of the same underlying trigger.
- If two situations share the same core need, emotional state, or context, keep only the most specific one and replace the other with a genuinely new situation.
- **If two cards share the same first clause — the same thing blocking the person — they are one card**, however differently the second clause is worded. Merge them, and use the freed slot for a different scene or return fewer cards.
- Before finalizing your output, review all situations together and ensure none feel redundant or too similar.

**Self-check: For each pair of situations, ask "Could a different person be the main actor, or is the setting/activity fundamentally different?" If the answer is NO, one of them must be replaced.**

# 7W Coverage (Soft Quota) — Make Diversity Real

{{coverage_section}}

# Format

Each situation: { "situation": "...", "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "evidence": "...", "section_refs": [<section_number>, ...] }

**7W's Framework** — Use these 7 dimensions as inspiration for diverse CEP situations:
- **Why** (Need/Motivation): The core reason or problem-solving need → Expressed as nanoIntents
- **When** (Occasion/Time): The timing or specific occasion that triggers category recall
- **Where** (Location/Context): Where the consumer is, or where they are using the product
- **While** (Parallel Activity): What else the consumer is doing at the same time
- **With Who** (Social Context): Who the consumer is with
- **With What** (Complementary Products): Other products or services paired with this category
- **hoW Feeling** (Emotional State): The consumer's mood or desired emotional state

**Nano Intent (nanoIntents array, exactly 3 items)**: Concrete purposes that differ per consumer within the same CEP (= Why dimension).
- Do NOT repeat product name or category
- Avoid generic phrases like "need it", "ran out of it"
- Each intent must be consistent with the evidence quotation — do not introduce a purpose the source never mentions
- Output exactly 3 nanoIntents per situation

{{evidence_section}}

# Language

**Please respond in {{response_language}} with valid JSON format.**

# Quantity Constraint

Aim for {{requested_count}} CEP objects.

**Grounding outranks the count.** If the Product Research Results do not support {{requested_count}} distinct, grounded situations, return fewer. Never invent a situation, a detail, or a quotation to reach the number, and never split one situation into several rephrased cards to pad it out.

Returning eight well-grounded cards is a better outcome than ten with two invented ones.

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- Return ONLY a single valid JSON value.
- Do NOT wrap the JSON in Markdown code fences (no ```).
- Do NOT add any prose, explanation, headings, or bullet/numbered lists outside JSON.
- Do NOT add trailing commas.
- Use double quotes for ALL JSON keys and string values.
- Top-level JSON MUST be an array with **at most** {{requested_count}} items.
````

---

### 배포 시 확인 사항

1. **P5.5 v.1.0.4와 함께 올릴 것.** v.1.0.3 원칙 3은 "쇼핑·깨달음으로 끝나는 절은 잘라내라"고 지시합니다.
   P4만 올리면 여기서 복원한 연결절을 5.5가 도로 잘라내고, 결과는 v.1.9.0과 같아집니다.
2. **P3 v.1.5.0이 이미 올라가 있어야 함.** 이 프롬프트는 "번호 문단마다 원문 인용이 들어 있는" 리서치를 전제합니다.
   릴리즈 D가 그 산출물이므로 확인만 하면 됩니다.
3. **배열 길이 검증 완화.** v.1.9.0 이월 사항. `length === requested_count`를 강제하고 있다면 `<=`로.
4. **주입 문구 3종 점검.** `{{task_section}}` · `{{coverage_section}}` · `{{evidence_section}}`.
   특히 `{{task_section}}`에 "짧게 쓰라"는 취지의 문구가 들어 있으면 이번 개정과 충돌합니다.
5. **1회차 지표는 두 개를 같이 본다.** `sentence-form.js`(문장 형태)와 `phase-monitor.js`(출처 소비자유형 %).
   앞이 좋아지고 뒤가 나빠지면 실패입니다.
