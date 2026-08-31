<!-- v.1.0.1_phase5-1_AI 챗봇 예시 질문 생성_KR_0831.md (updated 2026-08-31, rev.2) -->
<!-- 베이스: v.1.0.0_ KBF User Prompts (AI 챗봇 예시 질문 생성)_KR_0706.md
     rev.1(08-31 오전) — 나노인텐트 제거, 질문 개수 9 → 4
     rev.2(08-31 오후) — 4문 세트를 "변수 통제 설계"로 재정의.
                         스펙 명사 → 편익 조건 변환 규칙, 예/아니오 질문 금지,
                         1번 질문의 카테고리명 금지, 요청 동사 분화를 신설.
                         + 입력 변수 이름을 어드민 슬롯의 샘플 데이터 기준으로 정정
                           ({{cep}}→{{cep_situation}}, {{kbf}}→{{kbfs_text}}).
                           구 이름으로는 어드민 등록이 거부된다.
     ⚠ 백엔드 정합 필요: 응답 배열 길이 검증값 9 → 4
     ※ rev.2에서 변수 개수·출력 길이 변동 없음(이름만 정정 + product_categories_text 사용). -->

## 8. Phase 5-1 — KBF User Prompts (AI 챗봇 예시 질문 생성)

### 목적

하나의 CEP에 대해 질문 하나를 던지는 게 아니라, **KBF를 한 번에 하나씩만 투입한 4문 세트**를 만듭니다.
AI 답변에서 브랜드 호출도를 KBF 축별로 분리해 읽기 위한 **변수 통제 설계**입니다.

- **CEP는 상수**로 고정합니다. 4문 모두 같은 상황 안에 머뭅니다.
- **KBF는 한 번에 하나만** 투입합니다. 세 개를 한 문장에 몰아넣으면 어느 조건 때문에 브랜드가 들어왔는지/빠졌는지 분리할 수 없습니다.
- 비교형 프롬프트(KBF 3개 동시)는 이 세트와 **별도로** 운용합니다. 여기서 만들지 않습니다.

| 순번 | 구성        | 역할                                                                                        |
| ---- | ----------- | ------------------------------------------------------------------------------------------- |
| 1    | CEP 단독    | **기준선(baseline)**. CEP만 주어졌을 때 AI가 스스로 꺼내는 후보군과 판단 기준을 본다        |
| 2    | CEP × KBF1  | 1번 대비 후보군의 변화 = KBF1 하나의 순효과                                                 |
| 3    | CEP × KBF2  | 〃 KBF2의 순효과                                                                             |
| 4    | CEP × KBF3  | 〃 KBF3의 순효과                                                                             |

### 이 세트로 읽어내는 것

| 관측                                     | 해석                                                        |
| ---------------------------------------- | ----------------------------------------------------------- |
| 1번에 자사 있음 + 2~4번에 없음           | 카테고리 인지는 있으나 조건별 근거(RTB)가 없음              |
| 1번에 없음 + 2~4번에 있음                | 특정 기준의 강점은 있으나 상황 자체와 연결이 약함           |
| 1번 답변이 우리 KBF를 언급하지 않음      | 그 KBF가 아직 시장 표준 기준이 아님 → 선점 기회 또는 무관심 |
| 특정 번호에서만 경쟁사가 반복 등장       | 그 KBF는 경쟁사가 이미 점유 중                              |

### 개정 이력

| 버전          | 날짜         | 변경                                                                                                                                            |
| ------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| v.1.0.1 rev.2 | 08-31 (오후) | 스펙 명사 → 편익 조건 변환 규칙 신설 / 예·아니오 질문 금지 / 1번 질문의 카테고리명·해법 지정 금지 / 요청 동사 분화 / **입력 변수 이름 정정** |
| v.1.0.1 rev.1 | 08-31 (오전) | 입력 변수 `{{nano_intent}}`와 템플릿 내 Nano Intent 참조 제거. 출력 9개(KBF당 3개) → **4개**로 변경                                              |
| v.1.0.0       | 07-06        | 최초 판본                                                                                                                                       |

> **rev.2를 낸 이유.** rev.1에는 `Do NOT paste the KBF wording verbatim` 한 줄이 있었지만 pid 1620·1621의 20장 전부에서 무시됐습니다
> (`TPU 같은 방수 코팅`, `방수 멤브레인`, `탄성 밴드`, `57도`, `조절식 DPI`).
> 금지만 있고 **무엇으로 바꿔야 하는지**가 없었기 때문입니다. rev.2는 변환의 목적지를 규정합니다.

### 입력 변수

⚠ **v.1.0.0 · rev.1의 변수 표는 틀렸습니다.** `{{cep}}` · `{{kbf}}` 로는 어드민에 등록되지 않습니다.
아래는 어드민 슬롯의 샘플 데이터에 실제로 선언된 키입니다.

| 변수                          | 형태                                                          | 샘플값                                             |
| ----------------------------- | ------------------------------------------------------------- | -------------------------------------------------- |
| `{{cep_situation}}`           | CEP 상황 한 문장                                              | `출근길 지하철에서 음악을 들을 때`                 |
| `{{kbfs_text}}`               | **번호 매긴 여러 줄 목록** (`", "` 연결 문자열이 아님)        | `  1. 노이즈캔슬링\n  2. 배터리 지속시간\n  3. 착용감` |
| `{{product_categories_text}}` | 카테고리명 목록(쉼표 구분). **1번 질문에서 금지어로 쓴다**    | `무선 이어폰, 헤드폰`                              |
| `{{response_language_label}}` | 응답 언어 라벨                                                | `Korean` / `Japanese` / `English`                  |

샘플 데이터에는 `{{nano_intents_text}}` 도 남아 있지만 **참조하지 않습니다.** 나노인텐트는 Phase 4에서 제거됐습니다.
(프롬프트가 안 쓰는 키가 샘플에 남아 있는 것은 등록을 막지 않습니다.)

**KBF 목록의 순서가 곧 질문 순서입니다.** `{{kbfs_text}}`에 1·2·3으로 번호가 매겨져 나오고, 그 번호가 그대로 2·3·4번 질문에 대응합니다.

### 출력 형식

JSON 배열 (정확히 4개)

```json
[
  "CEP 단독 — 기준선 질문",
  "CEP × KBF1 질문",
  "CEP × KBF2 질문",
  "CEP × KBF3 질문"
]
```

KBF가 3개 미만이면 `1 + KBF 개수` 만큼만 출력합니다(KBF 2개 → 3개 항목). KBF가 3개를 넘으면 앞의 3개만 사용합니다.

### 요청 모델 및 파라미터

| 파라미터  | 설정값                                       | 비고                       |
| --------- | -------------------------------------------- | -------------------------- |
| model     | `'gpt-5.4-nano'`                             | 고정                       |
| input     | `buildKbfUserPromptsPrompt(...)` 결과 문자열 | CEP / KBF / 출력 언어 반영 |
| text      | `{ format: { type: 'text' } }`               | 일반 텍스트 출력           |
| reasoning | `{ effort: 'none' }`                         | 추론 effort 최소           |

### 코드 위치

- 상수: `KBF_USER_PROMPTS_TEMPLATE`
- 코드 위치: `app/module/clients/cep_prompts.py:963`
- 호출 API: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt 템플릿

```
<!-- v.1.0.1_phase5-1_AI 챗봇 예시 질문 생성_KR_0831.md rev.2 (updated 2026-08-31) -->
# Role

You write the questions a real consumer would type into an AI chatbot (ChatGPT, Gemini, Perplexity).
You are NOT writing a product spec query. You are writing what the person says when they are still deciding.

# What this set is for

This is a controlled-variable set. The CEP is the constant; one KBF is injected at a time.
Question 1 is the baseline — it shows which candidates and criteria the AI raises on its own.
Questions 2-4 each add exactly ONE KBF, so the change from question 1 is that KBF's isolated effect.
If you merge two KBFs into one question, the whole measurement is destroyed.

# Input

CEP (Category Entry Point):
{{cep_situation}}

KBFs (Key Buying Factors), numbered:
{{kbfs_text}}

Product category names (for rule A only):
{{product_categories_text}}

The KBF list is numbered. Item 1 is KBF1, item 2 is KBF2, item 3 is KBF3.

# The 4 questions — fixed order, do NOT change it

1. The CEP situation alone. No KBF.
2. The CEP situation + KBF1.
3. The CEP situation + KBF2.
4. The CEP situation + KBF3.

# Rule A — Question 1 (the baseline)

- Do NOT copy the CEP sentence. The CEP is written in an observer's voice ("~할 때" / "when the consumer ...").
  Rewrite it as the person's own words: **state the situation, then make a request.**
  ("~해서 ~하고 싶어. 어떤 걸 찾아봐야 할까?")
- Do NOT mention any KBF.
- **Do NOT use any word from the product category names given above, and do NOT name the product type at all.**
  Leave it open ("어떤 제품을 찾아봐야 할까").
  Naming the category forces the answer to stay inside that category, and you lose the competition
  against adjacent categories — which is exactly what this baseline is meant to reveal.
- Do NOT presuppose that the person already owns the product, and do NOT name a solution.

# Rule B — Questions 2-4

Each of these has the same three-part shape:

  [restate the CEP briefly]  →  [the KBF as a consumer-side condition]  →  [the request]

## B-1. Turn the spec into a benefit condition. This is the most important rule.

A KBF is usually written as a material name, a part name, a structure name, or a figure.
**If you paste that word into the question, the question dictates its own answer.**
"TPU 커버 추천해줘" has already narrowed the candidates to TPU products, so you can no longer measure
whether a brand is summoned *by the situation*. You are only measuring spec awareness.

So: replace the spec with **what that spec does for this person in this CEP situation.**

Never write these into the question:
- material / chemical names (TPU, 폴리우레탄, 실리콘, 라미네이트)
- part or component names (멤브레인, 엘라스틱 밴드, 팜레스트, 쉘)
- figures, angles, grades, ratings (57°, pH 5.5, 300g, IPX)
- industry jargon (회내, DPI, 통기 계수)

Constraints on the conversion:
- Convert only into the benefit that spec delivers **inside this CEP situation**. Stay in the situation.
- Do NOT invent a new figure, certification, standard, or test name that was not given to you.
- The reader must still be able to tell which KBF this question is about.

## B-2. Vary the request verb across the axes.

- **Verification request** — "…어떻게 확인하면 될까?", "…무슨 기준으로 골라야 해?"
  Use this when the KBF is something a buyer can check on a product page, a label, or in reviews.
  The AI's answer then lists what it treats as evidence for that KBF — that list is the point.
- **Recommendation request** — "…추천해줘", "…어떤 제품을 봐야 해?"
  Use this when asking *how to verify* would make the answer drift into usage tips instead of candidates
  (fit, grip, hold, feel, comfort).
- Default: questions 2 and 3 are verification requests, question 4 is a recommendation request.
  You may swap them if the KBF's nature calls for it, but:
  **at least one of questions 2-4 must be a recommendation request, and all three must not end the same way.**

## B-3. Never write a yes/no question.

"…있으면 도움이 될까?", "…하면 안전할까?", "…쓰면 괜찮을까?" can be answered with an explanation
and no candidates at all. Every question must end as an **open request**.

- ❌ "방수 멤브레인이 있으면 침구가 안전할까?"
- ✅ "액체가 매트리스 속까지 스며들지 않는 제품인지 어떻게 확인하면 될까?"

# Rule C — Applies to all four

- One question covers exactly ONE KBF. Never mix two.
- All four stay inside the same CEP situation. Do NOT invent a new situation.
- No brand names and no company/product names (non-branded prompts only).
- Questions 2-4 may name the product category; question 1 may not.
- Do NOT invent a separate purpose, motivation, or nano-intent axis. The CEP and the KBFs are the only sources.
- Vary the sentence structure so the four questions do not read as one template.
- Tone: friendly and casual, the way a person actually types.
- If there are fewer than 3 KBFs, produce only (1 + number of KBFs) items. If there are more than 3, use only the first 3.

# Worked example — shape only

⚠ This example is about a different product. Do NOT reuse any word, material, or condition from it.

CEP: 지하철로 출퇴근하면서 주변 소음 때문에 통화가 자꾸 끊길 때
KBFs: 1. 통화용 빔포밍 마이크  2. 40dB급 능동 소음 저감  3. 6시간 이상 연속 재생

Spec → benefit conversion:

| KBF (spec)              | what goes into the question                       | why                                                      |
| ----------------------- | ------------------------------------------------- | -------------------------------------------------------- |
| 통화용 빔포밍 마이크    | "시끄러운 데서도 내 목소리만 또렷하게 전달되는"   | 마이크 방식은 수단, 목소리 전달이 결과                    |
| 40dB급 능동 소음 저감   | "지하철 소음이 확 줄어드는"                       | 수치를 물으면 답이 사양 설명으로 끝남                     |
| 6시간 이상 연속 재생    | "왕복 출퇴근 내내 충전 없이 버티는"               | 시간 표기는 스펙, 하루를 버티는지가 편익                  |

[
  "출퇴근할 때 지하철이 시끄러워서 통화가 자꾸 끊기는데, 이럴 땐 어떤 걸 찾아봐야 할까?",
  "지하철에서 통화할 때 시끄러운 데서도 내 목소리만 또렷하게 전달되는 제품을 찾는데, 어떻게 확인하면 될까?",
  "지하철 소음이 확 줄어드는 이어폰을 고르려면 무슨 기준으로 봐야 해?",
  "왕복 출퇴근 내내 충전 없이 버티는 이어폰 추천해줘."
]

# Before you answer, check

1. Question 1 has no category name, no KBF, and does not copy the CEP's "~할 때" ending.
2. No question contains a material name, part name, figure, or jargon term.
3. No question can be answered with 예/아니오.
4. Questions 2-4 do not all end with the same request verb, and at least one asks for a recommendation.
5. Exactly one KBF per question, in order.

Output language: {{response_language_label}}

Response format (output ONLY a JSON array, nothing else):
["question1", "question2", "question3", "question4"]
```
