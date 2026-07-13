<!-- v.1.1.0_cep_KR_0710.md (updated 2026-07-10) — grounding 개정: 입력을 Phase 3.5 증거 유닛으로 교체 -->

## Phase 4 — CEP Trigger Extraction (CEP 상황, 나노인텐트 생성) — v1.1.0 (Grounded)

### v1.0.0 대비 변경점

| 항목      | v1.0.0 (현행)                                        | v1.1.0 (본 문서)                                                             |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| 입력      | Phase 3 산문 마크다운 (`product_research_sections`)  | **Phase 3.5 증거 유닛 JSON** (`evidence_units`)                              |
| Task      | 산문에서 상황 추출 (자유 생성)                        | **증거 유닛 선택·결합으로 장면 조립** (유닛 quote 밖 표현 생성 금지)          |
| 근거 연결 | `evidence`(문장) + `section_refs`(섹션 번호)          | `evidence_unit_ids` + `source_ref` + **RTB(verbatim 재인용)**                |
| 출력      | `{situation, nanoIntents×3, evidence, section_refs}` | `{situation, w7, nanoIntents×3, kbf_hints, rtb, source_ref, evidence_unit_ids}` |
| 유지      | —                                                    | 다양성 제약 · 7W soft quota · 수량 제약(정확히 N개) · JSON-only 규칙 전부 유지 |

### 목적

Phase 3.5의 증거 유닛에서 **구체적인 CEP 상황과 나노인텐트를 조립**합니다.
v1.0.0과 달리 자유 생성이 아니라 **유닛 선택·결합**이므로, 카드의 모든 구체어(시간·장소·발화·행동)가 인용으로 역추적됩니다.

### 핵심 개념

- **CEP / 나노인텐트 / 7W's Framework**: v1.0.0과 동일
- **조립(assembly)**: 같은 `source_ref` 안의 유닛 1~3개를 결합해 하나의 장면 문장을 만드는 것. 서로 다른 섹션의 유닛 결합은 금지(맥락 합성 방지)
- **구체어 대응 원칙**: 카드에 등장하는 모든 시간·장소·발화·행동 표현은 결합한 유닛의 `quote` 안에 실재해야 함
- **4대 환각 유형 금지** (Phase 3.5 문서와 동일 정의): ① 시간 단정(Time assertion) ② 없는 장소(Invented place) ③ 가짜 인용(Fake quotation) ④ 없는 행동(Invented action)

### 입력 변수

| 변수                      | 설명                                     | 예시               |
| ------------------------- | ---------------------------------------- | ------------------ |
| `{{product_name}}`        | 제품명                                   | `버티컬 마우스`    |
| `{{country}}`             | 국가 코드                                | `kr`               |
| `{{evidence_units}}`      | **Phase 3.5 출력 JSON (증거 유닛 배열)** | (JSON)             |
| `{{requested_count}}`     | 추출할 CEP 상황 개수                     | `10`               |
| `{{category}}`            | [선택] 제품 카테고리                     | `마우스, 입력장치` |
| `{{existing_situations}}` | [선택] 기존 CEP 목록 (중복 방지용)       | (이미 생성된 목록) |

### 출력 형식

JSON 배열 (정확히 `{{requested_count}}`개)

```json
[
  {
    "situation": "재택근무로 혼자 오래 작업하면서 목·어깨 불편을 먼저 느끼고, 소파·침대 옆에서 노트북을 쓰다 손목이 몸쪽으로 꺾이는 게 반복돼 '마우스까지 인체공학적으로 못 맞춘다'고 느껴 교체를 떠올리는 순간",
    "w7": {
      "why": "재택 전환으로 노트북 중심 작업이 길어짐",
      "where": "소파·침대 옆(팔이 자연스럽게 내려가지 않는 자세)",
      "while_": "목·어깨에 이어 손목이 몸쪽으로 꺾이는 느낌이 반복됨",
      "how_feeling": "'마우스까지 인체공학적으로 못 맞춘다'는 답답함"
    },
    "nanoIntents": [
      "노트북 중심 자세에서 손목이 꺾이지 않는 그립으로 바꾸기",
      "목·어깨-손목 부담을 함께 줄이기",
      "책상 전체가 아니라 손에 닿는 기기부터 바꾸기"
    ],
    "kbf_hints": ["손목을 덜 꺾이게 하는 수직(핸드셰이크) 그립 각도"],
    "rtb": "\"소파나 침대 옆에 노트북을 두고 업무를 보다 보니, 손목이 몸쪽으로 꺾이는 느낌이 반복돼요\" (hankyung)",
    "source_ref": "§1",
    "evidence_unit_ids": [1, 2]
  }
]
```

### 요청 모델 및 파라미터

v1.0.0과 동일 (gpt-5.4-nano · tools [] · reasoning none · max_output_tokens 32768).

---

### Prompt 템플릿

````
# Role
당신은 Category Entry Point(CEP) 식별을 전문으로 하는 소비자 행동 분석가입니다.
당신은 상황을 지어내지 않습니다. 사전 검증된 증거 유닛으로부터 CEP 상황을 **조립**하며, 그 결과 출력물의 모든 구체적 디테일이 verbatim 인용으로 역추적됩니다.

{{category_section}}
# Evidence Units (사전 검증됨, verbatim 근거 기반)
각 유닛은 소비자 리서치에서 가져온 verbatim 인용과, 그 인용이 직접 뒷받침하는 w7 필드·나노인텐트 후보·KBF 힌트를 담고 있습니다.

{{evidence_units}}

# CEP Definition

CEP (Category Entry Point): 상황/트리거
CEP란 소비자가 특정 제품이나 서비스를 필요로 하거나 구매를 고려하게 만드는 구체적인 상황·맥락·단서를 말합니다. 예를 들어 "목이 마를 때", "영화관에 있을 때", "친구 선물을 사야 할 때"가 바로 그런 상황입니다.

# Task

{{task_section}}

**증거 유닛을 선택·결합**하여 정확히 {{requested_count}}개의 CEP 상황을 조립하세요:

1. 같은 `source_ref`(같은 섹션)를 공유하는 유닛 1~3개를 고르세요. 서로 다른 섹션의 유닛을 결합하지 마세요 — 이는 어떤 소비자도 말하지 않은 맥락을 지어내는 것입니다.
2. 선택한 유닛의 quote와 w7 필드에 존재하는 표현만 사용하여 `situation`을 하나의 자연스러운 장면 문장으로 작성하세요.
3. 유닛의 w7 필드를 카드의 `w7`로 병합하세요(유닛이 실제로 제공하는 필드만 — 빠진 차원을 채우지 마세요).
4. 유닛의 `nano_intent_candidates`에서 출발하여 정확히 3개의 `nanoIntents`를 작성하세요; 자연스러움을 위해 표현을 바꿀 수는 있으나 새로운 구체 정보를 도입해서는 안 됩니다.
5. 유닛의 `kbf_hints`를 (중복 제거하여) 그대로 전달하세요.
6. `rtb`는 선택한 유닛 중 가장 강력한 quote로 설정하고(verbatim, 인용부호와 함께, source 태그 포함), `source_ref`는 공유된 섹션으로, `evidence_unit_ids`는 선택한 유닛 id로 설정하세요.

# Grounding Constraint (CRITICAL)

`situation`의 모든 구체적 디테일 — 시간 표현·장소·인용된 발화·행동 — 은 선택한 유닛의 quote에 반드시 등장해야 합니다.
다음은 금지됩니다:
- **Time assertion**: quote에 없는 기간/시점(예: "첫 주", "주말" 추가).
- **Invented place**: quote에 없는 장소(예: "도서관" 추가).
- **Fake quotation**: 인용부호로 감싼 의역. 인용하려면 유닛의 quote에서 verbatim으로 복사하세요.
- **Invented action**: quote에 없는 행동(예: "다시 검색" 추가).
장면이 빈약하게 느껴지더라도 빈약한 채로 두세요 — 근거가 희박하더라도 뒷받침되는 카드가 풍부하게 지어낸 카드보다 낫습니다.

# Prioritize Natural, Real-World Situations

- 평범한 사람들이 일상에서 실제로 떠올릴 법한 상황을 작성하세요
- 억지스럽거나 지나치게 복잡해 인위적으로 느껴지는 상황을 피하세요
- 일상에서 현실적으로 일어날 수 있는 구체적이고 자연스러운 상황에 집중하세요

# Diversity Constraint — Each Situation Must Be Independent

- 각 상황은 확연히 다른 페르소나·맥락·삶의 순간을 나타내야 합니다.
- 동일한 근본 트리거를 단지 다르게 표현한 것에 불과한 상황을 생성하지 마세요.
- 하나의 섹션을 반복해서 재사용하기보다 여러 다른 섹션(source_ref)을 두루 다루는 것을 선호하세요.
- 출력을 확정하기 전에 모든 상황을 함께 검토하여 어느 것도 중복되거나 지나치게 유사하게 느껴지지 않도록 하세요.

**Self-check: 각 상황 쌍에 대해 "다른 사람이 주인공이 될 수 있는가, 아니면 배경·활동이 근본적으로 다른가?"를 물으세요. 답이 NO라면 둘 중 하나는 교체되어야 합니다.**

# 7W Coverage (Soft Quota) — Make Diversity Real

{{coverage_section}}

# Format

각 상황: { "situation": "...", "w7": { ... }, "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "kbf_hints": ["..."], "rtb": "...", "source_ref": "§N", "evidence_unit_ids": [<unit_id>, ...] }

**7W's Framework** — `w7` 객체의 차원(유닛이 뒷받침하는 것만 채우세요):
- **Why** (필요/동기) / **When** (계기/시간) / **Where** (장소/맥락) / **While** (병행 활동) / **With Whom** (사회적 맥락) / **With What** (보완 제품) / **hoW Feeling** (감정 상태)
- JSON keys: why / when / where / while_ / with_whom / with_what / how_feeling

**Nano Intent (nanoIntents 배열, 정확히 3개 항목)**: 같은 CEP 안에서도 소비자마다 다른 구체적 목적(= Why 차원).
- 제품명이나 카테고리를 반복하지 마세요
- "필요하다", "다 떨어졌다" 같은 일반적인 표현을 피하세요
- 상황마다 정확히 3개의 nanoIntents를 출력하세요

{{evidence_section}}

# Language

**{{response_language}}로 유효한 JSON 형식으로 응답해 주세요.**
`rtb`는 원본 인용의 언어를 verbatim으로 보존해야 합니다.

# Quantity Constraint (CRITICAL)

정확히 {{requested_count}}개의 CEP 객체를 반환해야 합니다.
최상위 JSON 배열의 길이는 정확히 {{requested_count}}여야 합니다.
증거 유닛이 {{requested_count}}개의 충분히 구별되는 상황을 뒷받침하지 못하더라도, 여전히 {{requested_count}}개를 반환하세요 — 다만 디테일을 지어내는 대신 덜 사용된 섹션에서 더 빈약한 카드를 만드세요.

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- 오직 하나의 유효한 JSON 값만 반환하세요.
- JSON을 Markdown 코드 펜스로 감싸지 마세요 (``` 금지).
- JSON 외부에 어떤 산문·설명·제목·불릿/번호 목록도 추가하지 마세요.
- 후행 쉼표를 추가하지 마세요.
- 모든 JSON 키와 문자열 값에 큰따옴표를 사용하세요.
- 최상위 JSON은 반드시 길이가 정확히 {{requested_count}}인 배열이어야 합니다.
````
