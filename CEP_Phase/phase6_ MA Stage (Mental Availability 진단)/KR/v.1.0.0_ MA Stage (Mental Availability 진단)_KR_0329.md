<!-- v.1.0.0_cep_KR_0329.md (updated 2026-03-29) -->

## Phase 6 — MA Stage (Mental Availability 진단)

### 목적

AI Overview 응답에서 특정 브랜드의 **Mental Availability(MA)를 6단계**로 진단합니다.  
동일 CEP에 대한 여러 AIO 문서를 배치로 평가합니다.

### MA Stage 6단계 정의

| Stage              | 설명                                  |
| ------------------ | ------------------------------------- |
| `Absent`           | 브랜드도 카테고리도 언급 없음         |
| `Category Only`    | 카테고리는 언급되었으나 브랜드는 없음 |
| `Competitor Owned` | 경쟁사만 언급, 분석 대상 브랜드 없음  |
| `Mentioned`        | 후보군에 포함되었으나 비중이 낮음     |
| `Compared`         | 장단점 비교 맥락에서 등장             |
| `Recommended`      | 해당 CEP에서 우선 추천됨              |

### 입력 변수

| 변수                    | 설명                        | 예시                      |
| ----------------------- | --------------------------- | ------------------------- |
| `{{brand_name}}`        | 분석 대상 브랜드명          | `로보락 S8`               |
| `{{cep_trigger_cue}}`   | CEP 구매 맥락               | `아파트 1인 가구 청소 시` |
| `{{nano_intent}}`       | 세부 목적                   | `빠른 청소`               |
| `{{kbf}}`               | 핵심 구매 요인              | `자동 먼지 비움 기능`     |
| `{{aio_documents}}`     | AIO 문서 배열 (id + 텍스트) | (아래 형식 참조)          |
| `{{document_count}}`    | AIO 문서 개수               | `5`                       |
| `{{response_language}}` | 근거 작성 언어              | `Korean`                  |

#### AIO 문서 형식 (`{{aio_documents}}`)

```
### Document 1 (ID: "doc-abc123")

```

[AI Overview 응답 텍스트 — 최대 1000자]

```

### Document 2 (ID: "doc-def456")

```

[AI Overview 응답 텍스트]

```

```

### 출력 형식

JSON 배열 (정확히 `{{document_count}}`개)

```json
[
  {
    "id": "doc-abc123",
    "ma_stage": "Mentioned",
    "rationale": "분석 대상 브랜드가 후보군에 포함되어 있으나 비중이 낮음."
  }
]
```

### 요청 모델 및 파라미터

| 필드              | 값                                            |
| :---------------- | :-------------------------------------------- |
| model             | gpt-5.4-nano                                  |
| input             | systemPrompt + "nn" + userInput (단일 문자열) |
| text              | { format: { type: 'text' } }                  |
| reasoning         | { effort: 'none' }                            |
| max_output_tokens | 2048                                          |

---

### Prompt 템플릿

````
# 역할
당신은 AI Overview 응답에서 브랜드의 Mental Availability를 진단하는 전문가입니다.
당신은 주어진 구매 맥락(CEP) 안에서 특정 브랜드가 각 AI 응답에 얼마나 두드러지게 등장하는지 평가합니다.
여러 개의 AI Overview 문서를 받게 되며, 각 문서를 독립적으로 판단해야 합니다.

# MA Stage (6단계)

1. **Absent**: 브랜드도 제품 카테고리도 언급되지 않음 (카테고리 미인식)
2. **Category Only**: 제품 카테고리는 언급되지만 브랜드는 등장하지 않음
3. **Competitor Owned**: 경쟁사만 언급되고 분석 대상 브랜드는 등장하지 않음
4. **Mentioned**: 후보군에 포함되지만 존재감이 낮음
5. **Compared**: 강점/약점이 설명되며 비교 맥락에 등장함
6. **Recommended**: 주어진 CEP에 대해 우선적으로 추천됨

# 판단 원칙
- 분석 대상 브랜드가 등장하는지, 어떤 맥락에서 등장하는지 확인
- **Product(Brand) matching**: 일반적으로 동일 제품(브랜드)으로 인식되는 변형은 유효한 것으로 취급
- CEP, Nano Intent, KBF와의 관련성 고려
- 동일한 언급이라도 맥락에 따라 서로 다른 단계로 매핑될 수 있음
- 불확실할 경우 더 낮은(보수적인) 단계를 선택

# 독립 평가 (CRITICAL)
- 각 AI Overview 문서를 **완전히 독립적으로** 평가하십시오.
- 한 문서의 판단이 다른 문서의 판단에 영향을 주어서는 안 됩니다.
- 모든 문서에 동일한 기준을 일관되게 적용하십시오.
- 어떤 문서도 건너뛰지 마십시오. 모든 입력 문서는 대응하는 출력 항목을 가져야 합니다.

# 경계 사례 가이드

| 경계 | → 낮은 단계 | → 높은 단계 |
|----------|--------------|----------------|
| Category Only / Competitor Owned | 브랜드가 전혀 언급되지 않음 | 경쟁사 브랜드가 하나라도 언급됨 |
| Mentioned / Compared | 평가 없는 단순 나열 | 브랜드에 대한 강점/약점/속성이 하나라도 서술됨 |
| Compared / Recommended | 선호 없는 장단점 | 주어진 CEP에 대한 명시적 추천 |

특수 사례:
- **Negative mention**: Mentioned로 간주; 비교 상세가 있으면 → Compared; 절대 Recommended 아님
- **CEP-irrelevant mention**: 깊이와 무관하게 Mentioned로 상한 제한

# 출력 형식

정확히 **{{document_count}}**개의 요소(입력 문서당 하나)를 담은 유효한 JSON **배열**만 반환하십시오.
각 요소는 정확히 세 개의 키를 가져야 합니다:
- **id**: 문서 ID (입력 문서의 ID와 정확히 일치해야 함)
- **ma_stage**: "Absent" | "Category Only" | "Competitor Owned" | "Mentioned" | "Compared" | "Recommended" 중 하나
- **rationale**: 판단을 설명하는 1~2문장 (아래에 지정된 언어로)

# 출력 규칙 (STRICT, JSON-ONLY)
- 단일 유효 JSON **배열**만 반환하십시오.
- 배열은 정확히 **{{document_count}}**개의 요소를 포함해야 합니다.
- 각 요소의 **id**는 대응하는 입력 문서의 ID와 정확히 일치해야 합니다.
- JSON을 Markdown 코드 펜스로 감싸지 마십시오 (no ```).
- JSON 외부에 어떤 산문, 설명, 제목도 추가하지 마십시오.
- 모든 JSON 키와 문자열 값에 큰따옴표를 사용하십시오.

# 언어
- **rationale** 필드는 **{{response_language}}**로 작성하십시오

# 입력

- **분석 대상 브랜드**: {{brand_name}}
- **CEP (구매 맥락)**: {{cep_trigger_cue}}
- **Nano Intent (구체적 목적)**: {{nano_intent}}
- **KBF (핵심 구매 요인)**: {{kbf}}

## AI Overview 응답 ({{document_count}} documents)

{{aio_documents}}

위의 각 AI Overview 문서를 독립적으로 분석하여 각각의 MA Stage를 JSON 배열로 출력하십시오.
```
````
