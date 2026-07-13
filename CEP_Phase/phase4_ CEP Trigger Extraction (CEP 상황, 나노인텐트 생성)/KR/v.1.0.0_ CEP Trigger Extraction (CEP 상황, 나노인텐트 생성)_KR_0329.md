<!-- v.1.0.0_cep_KR_0329.md (updated 2026-03-29) -->

## Phase 4 — CEP Trigger Extraction (CEP 상황, 나노인텐트 생성)

### 목적

Product Research 결과에서 **구체적인 CEP(Category Entry Point) 상황과 나노인텐트**를 추출합니다.  
각 상황은 7W's Framework 기반으로 다양성이 보장되어야 합니다.

### 핵심 개념

- **CEP (Category Entry Point)**: 소비자가 특정 제품 카테고리를 필요로 하거나 떠올리게 되는 구체적 상황/트리거
- **나노인텐트 (Nano Intent)**: 동일 CEP 안에서 사람마다 갈라지는 구체적 목적 (=Why 차원, 정확히 3개)
- **7W's Framework**: When / Where / While / With Whom / With What / hoW Feeling / Why

### 입력 변수

| 변수                            | 설명                               | 예시               |
| ------------------------------- | ---------------------------------- | ------------------ |
| `{{product_name}}`              | 제품명                             | `탈모 샴푸`        |
| `{{country}}`                   | 국가 코드                          | `kr`               |
| `{{product_research_sections}}` | CEP Insight Research 결과 섹션들   | (마크다운 텍스트)  |
| `{{requested_count}}`           | 추출할 CEP 상황 개수               | `10`               |
| `{{category}}`                  | [선택] 제품 카테고리               | `샴푸, 헤어케어`   |
| `{{existing_situations}}`       | [선택] 기존 CEP 목록 (중복 방지용) | (이미 생성된 목록) |

### 출력 형식

JSON 배열 (정확히 `{{requested_count}}`개)

```json
[
  {
    "situation": "아침 샤워 중 배수구에 머리카락이 잔뜩 보일 때",
    "nanoIntents": [
      "탈모 초기인지 직접 확인해 보려고",
      "출근 전 간단히 관리할 방법이 궁금해서",
      "병원 가기 전에 일상 케어부터 시작하고 싶어서"
    ],
    "evidence": "2"
  }
]
```

### 요청 모델 및 파라미터

| 파라미터          | 설정값                                                              | 비고                               |
| :---------------- | :------------------------------------------------------------------ | :--------------------------------- |
| model             | 'gpt-5.4-nano'                                                      | 고정                               |
| input             | [{ role: 'user', content: [{ type: 'input_text', text: prompt }] }] | 단일 user 메시지 + input_text 파트 |
| text              | { format: { type: 'text' }, verbosity: 'low' }                      |                                    |
| reasoning         | { effort: 'none', summary: null }                                   |                                    |
| tools             | []                                                                  |                                    |
| store             | false                                                               |                                    |
| include           | []                                                                  |                                    |
| max_output_tokens | 32768                                                               |                                    |

---

### Prompt 템플릿

````
# 역할
당신은 Category Entry Point(CEP) 식별을 전문으로 하는 소비자 행동 분석가입니다.
당신은 소비자가 특정 제품 카테고리를 떠올리거나 필요로 하게 만드는 실제 삶의 상황, 트리거, 맥락을 밝혀내는 데 전문성이 있습니다.

{{category_section}}
# 제품 리서치 결과
{{product_research_sections}}

# CEP 정의

CEP (Category Entry Point): 상황/트리거
CEP는 소비자가 특정 제품이나 서비스를 필요로 하거나 구매를 고려하게 만드는 구체적인 상황, 맥락, 신호를 의미합니다. 예를 들어 "목이 마를 때", "영화관에 있을 때", "친구에게 줄 선물을 사야 할 때"가 바로 그러한 상황입니다.

# 작업

{{task_section}}

# 자연스럽고 현실적인 상황 우선하기

- **보통 사람들이 일상생활에서 실제로 떠올릴 법한 상황을 작성하세요**
- 억지스럽거나 지나치게 복잡해서 인위적으로 느껴지는 상황은 피하세요
- 일상에서 현실적으로 일어날 수 있는 구체적이고 자연스러운 상황에 집중하세요
- 실제 소비자가 실제로 검색하거나 떠올릴 만한 것을 생각하세요

# 다양성 제약 — 각 상황은 독립적이어야 함

- **각 상황은 반드시 뚜렷하게 서로 다른 페르소나, 맥락, 또는 삶의 순간을 나타내야 합니다.**
- 동일한 근본 트리거를 단순히 다르게 표현한 상황을 생성하지 마세요.
- 두 상황이 동일한 핵심 니즈, 감정 상태, 또는 맥락을 공유한다면, 가장 구체적인 하나만 남기고 나머지는 진정으로 새로운 상황으로 교체하세요.
- 출력을 확정하기 전에 모든 상황을 함께 검토하여 중복되거나 지나치게 유사하게 느껴지는 것이 없는지 확인하세요.

**자체 점검: 각 상황 쌍에 대해 "다른 사람이 주된 행위자가 될 수 있는가, 아니면 배경/활동이 근본적으로 다른가?"를 자문하세요. 답이 '아니오'라면 둘 중 하나는 반드시 교체해야 합니다.**

# 7W Coverage (Soft Quota) — 다양성을 실질화하기

{{coverage_section}}

# 형식

Each situation: { "situation": "...", "nanoIntents": ["<intention1>", "<intention2>", "<intention3>"], "evidence": "...", "section_refs": [<section_number>, ...] }

- **section_refs**: 이 CEP 상황을 직접 뒷받침하는 제품 리서치 결과의 section_number 값들. 1~3개의 섹션 번호를 포함하세요. 해당하는 특정 섹션이 없으면 빈 배열 []을 사용하세요.

**7W's Framework** — 다양한 CEP 상황을 위한 영감으로 다음 7가지 차원을 사용하세요:
- **Why** (니즈/동기): 핵심 이유 또는 문제 해결 니즈 → nanoIntents로 표현됨
- **When** (계기/시간): 카테고리를 떠올리게 하는 타이밍 또는 특정 계기
- **Where** (장소/맥락): 소비자가 있는 곳, 또는 제품을 사용하는 곳
- **While** (병행 활동): 소비자가 동시에 하고 있는 다른 활동
- **With Who** (사회적 맥락): 소비자가 함께 있는 사람
- **With What** (보완 제품): 이 카테고리와 함께 사용되는 다른 제품이나 서비스
- **hoW Feeling** (감정 상태): 소비자의 기분 또는 원하는 감정 상태

**Nano Intent (nanoIntents 배열, 정확히 3개)**: 동일한 CEP 안에서 소비자마다 달라지는 구체적인 목적(= Why 차원).
- 제품명이나 카테고리를 반복하지 마세요
- "필요하다", "다 떨어졌다" 같은 일반적인 표현은 피하세요
- 각 상황마다 정확히 3개의 nanoIntents를 출력하세요

{{evidence_section}}

# 언어

**{{response_language}}로 유효한 JSON 형식으로 응답해 주세요.**

# 수량 제약 (CRITICAL)

정확히 {{requested_count}}개의 CEP 객체를 반환해야 합니다.
최상위 JSON 배열의 길이는 정확히 {{requested_count}}개여야 합니다.

---

# 출력 규칙 (STRICT, JSON-ONLY)
- 오직 하나의 유효한 JSON 값만 반환하세요.
- JSON을 Markdown 코드 펜스로 감싸지 마세요 (``` 사용 금지).
- JSON 외부에 어떠한 산문, 설명, 제목, 또는 불릿/번호 목록도 추가하지 마세요.
- 후행 쉼표를 추가하지 마세요.
- 모든 JSON 키와 문자열 값에 큰따옴표를 사용하세요.
- 최상위 JSON은 반드시 길이가 정확히 {{requested_count}}인 배열이어야 합니다.
```
````
