<!-- v.1.0.0_cep_KR_0329.md (updated 2026-03-29) -->

## Phase 7-1 — 유저 프롬프트 생성

### 목적

CEP마다 Nano Intent·KBF·카테고리를 넣어 질문 1개 생성 (AI 검색용 자연어)

### 입력 변수

| 필드              | 타입     | 필수                   | 서버에서 하는 처리         |
| :---------------- | :------- | :--------------------- | :------------------------- | --------- | ------------------------------------------------------------------- |
| cepSituation      | string   | ✅                     | 공백 제거, 비어 있으면 400 |
| nanoIntents       | string[] | 기본 []                | 문자열만, trim, 최대 3개   |
| kbfs              | string[] | ✅ (비어 있으면 안 됨) | 문자열만, trim, 최대 3개   |
| productCategories | string[] | 기본 []                | 문자열만, trim             |
| country           | 'kr'     | 'jp'                   | 'us' 등                    | 기본 'kr' | responseLocale이 없을 때 보조로 사용                                |
| responseLocale    | 'ko'     | 'en'                   | 'ja'                       | 선택      | 있으면 lang 결정에 우선 (en/ja면 그대로, 아니면 country로 ja/en/ko) |

### 요청 모델 및 파라미터

| 항목      | 값                                                                                   |
| :-------- | :----------------------------------------------------------------------------------- |
| model     | 'gpt-5.4-nano'                                                                       |
| input     | buildCepUserPromptsPrompt(...) 결과 단일 문자열 (user 메시지 분리 없이 통째로 input) |
| text      | { format: { type: 'text' } }                                                         |
| reasoning | { effort: 'none' }                                                                   |

---

### Prompt 템플릿

```
아래의 CEP(Category Entry Point) 상황, Nano Intent, KBF(Key Buying Factor)를 사용해 실제 사용자가 AI 챗봇(ChatGPT, Gemini, Perplexity 등)에게 물어볼 법한 자연스러운 질문을 생성하세요.

**중요 규칙:**
1. 주어진 CEP 상황을 자연스럽게 변형하세요.
2. 아래에 제공된 모든 KBF를 자연스럽게 녹여낸 종합적인 질문을 정확히 1개만 생성하세요.
3. 질문에는 CEP 상황의 맥락이 담겨야 하며, 모든 Key Buying Factor를 자연스럽고 대화하듯 엮어내야 합니다.
4. 제품 카테고리를 자연스럽게 녹여내세요.
5. 기술적인 제품 사양이나 정확한 KBF 용어를 사용하지 마세요. 대신 일반 사용자가 자연스럽게 쓸 법한 일상적인 표현으로 특징을 설명하세요.
6. 정확한 제품 특징은 모르지만 자신의 상황에서 무엇이 필요한지는 아는 일반 소비자의 관점에서 작성하세요.
7. 도움이 되는 친구와 이야기하듯 자연스럽고 대화체로 작성하세요.
8. 여러 구매 요인이 끊긴 목록이 아니라 하나의 응집된 질문 안에서 자연스럽게 흐르도록 하세요.

**예시:**
- CEP 상황: 출근 전에 아침 식사를 해결해야 할 때
- Nano Intent: "바삭함 유지", "빠르게 만들기", "요리를 루틴으로 만들기"
- KBF 1: "강화유리 도어"
- KBF 2: "빠른 가열 기술"
- KBF 3: "간편 세척 코팅"

- 나쁜 예: "퇴근 후 저녁 식사를 해결해야 할 때, 강화유리 도어와 빠른 가열, 간편 세척이 되는 에어프라이어를 찾고 있어요."
  -  ❌ CEP 상황을 완전히 바꿔버림.
  -  ❌ 기술적인 제품 사양이나 정확한 KBF 용어를 사용함.
  -  ❌ 자연스러운 흐름 없이 특징을 나열함.

- 좋은 예: "바쁜 준비 시간에 출근 전 아침을 해결해야 해요. 빨리 데워져서 후딱 요리할 수 있고, 안이 잘 보여서 익은 정도를 실시간으로 확인할 수 있고, 다 쓴 뒤엔 쓱 닦기 쉬운 에어프라이어를 원해요. 어떤 제품이 있을까요?"
  - ✅ CEP 상황 포함 ("출근 전 아침을 해결")
  - ✅ Nano Intent 포함 ("바쁜 준비 시간에", "후딱 요리")
  - ✅ 모든 KBF 초점을 기술 용어 없이 자연스럽게 포함 ("빨리 데워져서", "안이 잘 보여서", "쓱 닦기 쉬운")
  - ✅ 제품 카테고리 포함 ("에어프라이어")
  - ✅ 여러 니즈가 하나의 응집된 질문 안에서 자연스럽게 흐름

질문은 완전한 이야기를 담아야 합니다: 사용자의 상황(주어진 CEP), 이루고자 하는 것(Nano Intent), 그리고 필요한 제품 특징(모든 KBF를 자연스럽게 엮은 것).
AI 검색에서 흔히 나타나는 의도가 강하고 복합적인 프롬프트에 집중하세요.
톤: 도움이 되는 또래 친구와 이야기하듯 편하고 친근하며 캐주얼하고 접근하기 쉬운 스타일로 작성하세요.

Input:
- CEP Situation: ${cepSituation}
- Nano Intents:
${nanoIntentsText}
- KBFs (Key Buying Factors) - ALL must be included in the question:
${kbfsText}
- Product Categories: ${productCategoriesText}

Output language: ${langLabel}

Response format (output ONLY a JSON array with ONE question, nothing else):
["comprehensive question that includes all KBFs"]
```
