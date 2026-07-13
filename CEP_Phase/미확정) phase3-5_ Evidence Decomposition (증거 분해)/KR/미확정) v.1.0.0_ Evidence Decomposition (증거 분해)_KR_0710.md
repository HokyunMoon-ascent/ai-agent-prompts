<!-- v.1.0.0_cep_KR_0710.md (updated 2026-07-10) -->

## Phase 3.5 — Evidence Decomposition (증거 분해)

### 목적

Phase 3(CEP Insight Research)·Phase 3-1(Latent Intent)의 웹서치 산문에서 **verbatim 인용을 구조화된 '증거 유닛(evidence unit)'으로 분해**합니다.
이후 단계(Phase 4 CEP 조립, Phase 5 KBF 확정)는 이 유닛 **밖의 표현을 새로 만들 수 없게** 하여, 소스에 없는 구체화(환각)를 생성 단계에서 차단하는 grounding 단계입니다.

> 배경: Hallucination_test 검증(예: project 1328)에서 fail/warning 카드의 원인은 모두
> "산문 리서치 → 카드 직행" 과정에서 생긴 **소스에 없는 구체화**였음.
> 사후 repropose에서 수행하던 "검증 인용만으로 w7·nano_intent·KBF·RTB 분해 + source_ref 명시"를
> 파이프라인의 정식 단계로 이동한 것이 본 Phase.

### 핵심 개념

- **증거 유닛 (Evidence Unit)**: 본문에서 verbatim으로 발췌한 quote 1개를 중심으로, 그 quote가 직접 뒷받침하는 w7 부분필드·나노인텐트 후보·KBF 힌트·RTB·source_ref를 묶은 최소 근거 단위
- **verbatim**: 본문 문장을 글자 그대로 복사한 것. 의역·요약·합성 금지
- **RTB (Reason To Believe)**: 소비자/독자에게 재인용 가능한 형태로 다듬은 quote (내용 추가 없이 트리밍만 허용)
- **source_ref**: quote가 속한 본문 섹션 번호 (`§N` 표기)
- **4대 환각 유형** (검증에서 반복 확인된 패턴 — 본 단계에서 명시적 금지):
  1. **시간 단정** — 소스에 없는 기간/시점 특정 (예: "첫 주", "주말")
  2. **없는 장소** — 소스에 없는 장소 추가 (예: "도서관")
  3. **가짜 인용** — 의역을 따옴표로 감싸 실재 발화처럼 표기
  4. **없는 행동** — 소스에 없는 행동 서술 (예: "다시 검색")

### 입력 변수

| 변수                            | 설명                                                     | 예시              |
| ------------------------------- | -------------------------------------------------------- | ----------------- |
| `{{product_name}}`              | 제품명                                                   | `버티컬 마우스`   |
| `{{response_language}}`         | 응답 언어                                                | `Korean`          |
| `{{product_research_sections}}` | Phase 3 결과 (섹션 번호 포함 마크다운)                   | (마크다운 텍스트) |
| `{{latent_intent_sections}}`    | [선택] Phase 3-1 결과 — 있으면 함께 분해                 | (마크다운 텍스트) |
| `{{web_sources}}`               | [선택] Phase 3 `web_search_call.action.sources` 목록     | (url + title)     |

### 출력 형식

JSON 배열 (증거 유닛 목록 — 개수 제한 없음, 본문이 뒷받침하는 만큼)

```json
[
  {
    "unit_id": 1,
    "source_ref": "§1",
    "quote": "소파나 침대 옆에 노트북을 두고(팔이 자연스럽게 내려가지 않는 자세로) 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요.",
    "url": "https://www.hankyung.com/article/2021010599531",
    "url_title": "버티컬 마우스로 손목 건강 챙긴다 | 한국경제",
    "w7": {
      "where": "소파·침대 옆(팔이 자연스럽게 내려가지 않는 자세)",
      "while_": "손목이 몸쪽으로 꺾이는 느낌이 반복됨"
    },
    "nano_intent_candidates": [
      "노트북 중심 자세에서 손목이 꺾이지 않는 그립으로 바꾸기"
    ],
    "kbf_hints": [
      "손목을 덜 꺾이게 하는 수직(핸드셰이크) 그립 각도"
    ],
    "rtb": "\"소파나 침대 옆에 노트북을 두고 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요\" (hankyung)"
  }
]
```

- `w7` 키: `why` / `when` / `where` / `while_` / `with_whom` / `with_what` / `how_feeling` — **quote가 직접 뒷받침하는 필드만 포함** (나머지 키는 생략, null 채우기 금지)
- `nano_intent_candidates`: 0~3개. quote의 내용만으로 표현 가능한 목적
- `kbf_hints`: 0~3개. quote에 언급/함의된 구체 속성만 (수치는 quote에 있을 때만)
- `url`/`url_title`: 본문 인용 링크 또는 `{{web_sources}}`에서 매칭. 불명이면 빈 문자열

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                              | 비고                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | 고정                               |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | 단일 user 메시지 + input_text 파트 |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  | 웹서치 없음 — 분해 전용            |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt 템플릿

````
# 역할
당신은 증거 큐레이션 전문가입니다. 당신의 임무는 소비자 리서치 텍스트를 **verbatim 증거 유닛(evidence unit)**으로 분해하는 것입니다 — 모든 필드가 소스 텍스트의 정확한 quote로 직접 뒷받침되는 구조화된 레코드입니다. 소스에 없는 정보는 절대 추가하지 않습니다.

# 소스 텍스트 (Product Research)
다음은 **"{{product_name}}"**의 웹 리서치 기반 소비자 컨텍스트입니다. 섹션 제목에는 번호가 매겨져 있으며(## N. ...), N을 섹션 참조로 사용하세요.

{{product_research_sections}}

{{latent_intent_block}}

# 웹 소스 (URL 매칭용)
{{web_sources_block}}

# 작업
소스 텍스트를 증거 유닛으로 분해하세요. 섹션별로, 문단별로 작업합니다:

1. 서로 구별되는 소비자 컨텍스트를 서술하는 각 문단마다, 증거 유닛을 하나만 추출합니다.
2. `quote` = 해당 문단에서 **verbatim**으로 복사한 문장. 정확히 복사하세요 — 의역, 요약, 서로 다른 문단의 문장 병합, 표현 "정리(clean up)"는 절대 하지 마세요.
3. `source_ref` = "§N", 여기서 N은 quote가 속한 섹션 번호입니다.
4. `url` / `url_title` = 소스 텍스트에서 해당 문단에 붙은 인용 링크(또는 웹 소스에서 가장 잘 맞는 것). 불명이면 ""를 사용하세요. URL을 절대 지어내지 마세요.
5. `w7` = quote 자체가 뒷받침하는 차원만, 가능한 한 quote 자신의 표현을 사용해 서술:
   - why (동기) / when (시간) / where (장소) / while_ (병행 활동) / with_whom / with_what / how_feeling
   - **quote가 뒷받침하지 않는 키는 생략하세요. 희소한(sparse) 유닛이 옳고, 억지로 채운 유닛은 틀립니다.**
6. `nano_intent_candidates` = quote가 말하는 내용만으로 진술할 수 있는 소비자 목적 0~3개. 제품/카테고리 이름을 반복하지 마세요. 일반적인 표현("필요하다", "다 떨어졌다")은 금지.
7. `kbf_hints` = quote에 언급되었거나 직접 함의된 구체적 제품 속성 0~3개(형태, 구조, 소재, 기능). 수치는 quote에 verbatim으로 나타날 때만 포함하세요.
8. `rtb` = quote를 재인용 가능한 형태로 다듬어 따옴표로 감싸고 짧은 소스 태그를 붙인 것. 트리밍만 허용 — 단어를 절대 추가하거나 바꾸지 마세요.

# 하드 그라운딩 규칙 (위반 시 해당 유닛 무효)
- quote는 소스 텍스트에 verbatim으로 존재해야 합니다.
- 모든 w7 값, 나노인텐트 후보, KBF 힌트는 그것이 속한 quote로 추적 가능해야 합니다 — 카테고리에 대한 당신의 일반 지식이 아니라.
- 다음 네 가지 환각 패턴은 출력 어디에서도 엄격히 금지됩니다:
  1. **시간 단정 (Time assertion)**: 소스가 진술하지 않은 기간/시점을 추가(예: "첫 주", "주말").
  2. **없는 장소 (Invented place)**: 소스가 언급하지 않은 장소를 추가(예: "도서관").
  3. **가짜 인용 (Fake quotation)**: 의역을 소스에서 발화/기술된 것처럼 따옴표로 감싸기.
  4. **없는 행동 (Invented action)**: 소스가 서술하지 않은 행동을 서술(예: "다시 검색").
- 문단이 너무 모호해 어떤 w7 필드도 뒷받침하지 못하면, quote + source_ref만으로 유닛을 출력하세요(빈 w7 객체 허용).

# 언어
`quote`/`rtb`을 제외한 모든 필드 값을 **{{response_language}}**로 작성하세요. `quote`/`rtb`은 소스 언어를 verbatim으로 보존해야 합니다.

# 출력 규칙 (엄격, JSON 전용)
- 유효한 단일 JSON 배열만 반환하세요.
- JSON을 마크다운 코드 펜스로 감싸지 마세요(``` 금지).
- JSON 밖에 어떤 산문, 설명, 제목도 추가하지 마세요.
- 후행 쉼표를 추가하지 마세요. 모든 키와 문자열 값에 큰따옴표를 사용하세요.
- `unit_id`는 숫자여야 하며, 1부터 시작하는 순차 번호입니다.
````

#### 템플릿 조립 규칙

| 블록                      | 값                                                                                                   |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| `{{latent_intent_block}}` | `latent_intent_sections`가 있으면 `# Source Text (Latent Intent)\n{{latent_intent_sections}}`, 없으면 빈 문자열 |
| `{{web_sources_block}}`   | `web_sources`가 있으면 url·title 목록, 없으면 `(none provided)`                                       |

---

### 부록 — Dry-run 예시 (project 1328 §1 실데이터)

Phase 3 산출물 §1의 실제 문단:

> 재택근무를 처음 시작했을 때(혼자 컴퓨터 앞에서 작업하는 시간이 길어질 때) 목·어깨 불편을 먼저 느끼고, 그다음엔 노트북 환경에서 "마우스까지 인체공학적으로 못 맞추는" 문제가 크게 와 닿아요. ([logitech.com](https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf))

이 문단이 만드는 증거 유닛:

```json
{
  "unit_id": 2,
  "source_ref": "§1",
  "quote": "재택근무를 처음 시작했을 때(혼자 컴퓨터 앞에서 작업하는 시간이 길어질 때) 목·어깨 불편을 먼저 느끼고, 그다음엔 노트북 환경에서 \"마우스까지 인체공학적으로 못 맞추는\" 문제가 크게 와 닿아요.",
  "url": "https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf",
  "url_title": "",
  "w7": {
    "why": "재택 전환으로 노트북 중심 작업이 길어짐",
    "while_": "목·어깨 불편을 먼저 느낌",
    "how_feeling": "'마우스까지 인체공학적으로 못 맞춘다'는 답답함"
  },
  "nano_intent_candidates": [
    "노트북 중심 자세에서 목·어깨-손목 부담을 함께 줄이기"
  ],
  "kbf_hints": [],
  "rtb": "\"노트북 환경에서 '마우스까지 인체공학적으로 못 맞추는' 문제가 크게 와 닿아요\" (logitech)"
}
```

주목할 점:
- `when`에 "첫 주" 같은 기간이 **없음** — 소스가 "처음 시작했을 때"까지만 말하므로 (AS-IS Phase 4는 여기서 "첫 주"를 만들어 warning을 받았음)
- `how_feeling`의 따옴표 인용이 소스 verbatim — AS-IS의 가짜 인용("손목도 마우스로 더 안 맞는 것 같다")이 원천 차단됨
- `kbf_hints`가 빈 배열 — 이 quote에는 제품 속성 언급이 없으므로 억지로 채우지 않음 (수직 그립 힌트는 §1의 hankyung quote 유닛에서 나옴)

이 유닛 2개(§1 logitech + hankyung)만으로 Phase 4가 조립한 결과가 `Hallucination_test/repropose/1328.json`의 **CEP 4 수정안**과 동일해지는 것이 본 단계의 목표 동작입니다.
