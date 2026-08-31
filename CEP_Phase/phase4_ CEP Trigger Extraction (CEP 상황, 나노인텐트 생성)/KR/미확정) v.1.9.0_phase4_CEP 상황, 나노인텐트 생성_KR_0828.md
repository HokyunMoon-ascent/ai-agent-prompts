<!-- 미확정) v.1.9.0_phase4_CEP 상황, 나노인텐트 생성_KR_0828.md (updated 2026-08-28) -->
<!-- v.1.0.0 기반 개정. 3단 분업(P3 원문 전달 → P4 CEP 변환 → P5.5 그라운딩)의 P4 몫.
     P3 v.1.5.0 과 짝입니다. 반드시 함께 배포하세요 — 한쪽만 올리면 1464(카드 1장) 사고가 재현됩니다.
     개정 4건: (1) CEP Definition 을 판정 게이트로 확장 (2) 빈 배열 허용 삭제 (3) evidence verbatim 의무화
               (4) 수량 절과 근거 절의 충돌 정리.
     ⚠ 백엔드 확인 필요: 배열 길이 검증이 '정확히 N'이면 'N 이하'로 완화해야 합니다(아래 주석 참조).
     ⚠ v.1.0.0 문서 내부 모순 미해결 이월: 머리 주석은 "나노인텐트 제거"라고 적혀 있으나 템플릿·출력 예시에는
       살아 있고, 릴리즈 C 번들에도 nano_intent 가 실제로 실려 나옵니다(290장 전량).
       스키마를 건드리지 않기로 한 이번 개정에서는 **살아 있는 쪽을 정본으로 보고 유지**했습니다. 제거 여부는 별도 결정 사항. -->

## Phase 4 — CEP Trigger Extraction (CEP 상황 생성)

### 목적

Product Research 결과에서 **구체적인 CEP(Category Entry Point) 상황**을 추출합니다.
모든 상황은 리서치 본문에 실재하는 근거로 뒷받침되어야 하며, 근거 없는 디테일 생성을 금지합니다.

**이 단계가 해석을 전담합니다.** Phase 3은 소비자의 말을 그대로 옮겨 오기만 합니다.
그 원문을 CEP 문장으로 바꾸는 일, 그리고 그것이 진입 장면인지 판정하는 일이 여기 몫입니다.

### v.1.0.0 대비 변경점

| #   | 무엇                                                             | 왜                                                                                                                                                                                                                                |
| --- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | `# CEP Definition`을 4개 판정 게이트로 확장                      | v.1.0.0 정의에는 "카테고리 밖에 있던 사람이 들어오는 입구"라는 조건이 없었다. 예시 3개(`when thirsty` 등)도 진입과 재구매를 구분하지 않는다. 0828 실측에서 이미 사용 중 29장, 제품·기능 탐색 14장, 1회성 사건 8장이 여기서 나왔다 |
| 2   | `If no specific section applies, use an empty array []` **삭제** | 인용 0건 카드 21장은 규칙 위반이 아니라 이 문장대로 나온 결과였다                                                                                                                                                                 |
| 3   | `evidence`를 P3 번호 문단의 **verbatim 인용**으로 한정           | 검수는 인용문과 리서치 본문을 대조한다. 의역하면 실재하는 근거도 unfounded 로 떨어진다                                                                                                                                            |
| 4   | 수량 절과 근거 절의 우선순위 명시                                | v.1.0.0은 "정확히 N개"를 CRITICAL로 걸어 두고 근거가 없어도 채우게 만들었다                                                                                                                                                       |
| 5   | 4대 환각 유형을 프롬프트 본문으로 승격                           | v.1.0.0에서는 문서 상단 '핵심 개념'에만 있고 템플릿에는 없었다                                                                                                                                                                    |

### 핵심 개념

- **CEP (Category Entry Point)**: 소비자가 특정 제품 카테고리를 필요로 하거나 떠올리게 되는 구체적 상황/트리거
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why — 다양성 점검용 관찰 렌즈 (강제 충족 쿼터 아님)
- **구체어 대응 원칙**: 상황에 등장하는 모든 시간·장소·발화·행동 표현은 제품 리서치 본문에 실재해야 함
- **4대 환각 유형 금지**: ① 시간 단정(Time assertion) ② 없는 장소(Invented place) ③ 가짜 인용(Fake quotation) ④ 없는 행동(Invented action)

### 입력 변수

v.1.0.0과 동일합니다. **신규 변수 없음.**

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
    "situation": "아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때",
    "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"],
    "evidence": "샤워하고 나면 배수구에 머리카락이 한 움큼씩 빠져 있어요",
    "section_refs": [2]
  }
]
```

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

> ⚠ **모델명을 이 문서에서 확정하지 않았습니다.** 릴리즈 C 번들에는 검수 모델(`inspection.model_info.model = gpt-5.4-nano`)만 기록되어 있고
> 단계별 모델은 실려 있지 않습니다. 폴더명의 "모델 상향" 라벨만으로는 P4에 무엇이 걸렸는지 확인되지 않으므로,
> 과금 기록에서 확인한 뒤 채워 넣으세요.

---

### Prompt 템플릿

````
<!-- 미확정) v.1.9.0_phase4_CEP 상황, 나노인텐트 생성_KR_0828.md (updated 2026-08-28) -->
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
3. **A life scene, not a search.** Something is happening in their life. "Comparing reviews", "looking into which model is better", "wondering whether to get one" are what happens *after* entry. End the sentence at the cause, not at the shopping.
4. **Recurring, not a one-off.** The situation comes back in ordinary life. A single accident, an injury, a move, a graduation happens once and passes — it is not an entry point.

Examples of what fails:
- ✗ "When the camera I installed still leaves a blind spot" — already owns it (fails 1)
- ✗ "When comparing reviews to decide which model is better" — already shopping (fails 2, 3)
- ✗ "When my wrist hurts and I start looking into a gentler input method" — ends in search behavior (fails 3)
- ✗ "Right after moving into a new place" — happens once (fails 4)
- ✓ "When there's no way to check whether the dog left home alone is doing okay" — a recurring life scene that brings the category to mind

# Task

{{task_section}}

# Writing the Situation Sentence

This is the one place where you transform rather than copy. Everything else you carry over unchanged.

- Turn the quoted circumstance into a single concrete moment, written in the consumer's everyday words.
- **Keep the concrete nouns the consumer used.** Replacing "the flat mouse they gave me at work" with "office equipment" produces a sentence that points at nothing. Words like "device", "item", "product", "solution" can mean anything, so they mean nothing.
- Do NOT name the category, a product, a brand, a model, or a feature in the situation sentence.
- Every concrete element in your sentence — time, place, action, spoken line, feeling — must trace back to the evidence quotation you cite.

# Evidence — Every Card Must Be Traceable

- `evidence`: copy the consumer's quotation **verbatim** from the numbered paragraph you used. Keep the original wording, spelling, and sentence ending. Do not paraphrase, shorten, or polish it. This string is matched against the research text downstream; any rewording makes a real citation register as unfounded.
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

1. **P3 v.1.5.0과 함께 올릴 것.** 이 프롬프트는 "번호 문단마다 원문 인용이 들어 있는" 리서치를 전제합니다. P3가 1.0.0이면 인용할 원문이 없어 evidence 규칙이 공회전합니다.
2. **배열 길이 검증 완화.** 백엔드가 `length === requested_count`를 강제하고 있다면 `<=`로 바꿔야 합니다. 안 바꾸면 근거 부족 시 파싱 실패로 떨어집니다.
3. **주입 문구 3종 점검.** `{{task_section}}` · `{{coverage_section}}` · `{{evidence_section}}`에 들어가는 실제 문구가 위 규칙과 충돌하지 않는지 확인하세요.
