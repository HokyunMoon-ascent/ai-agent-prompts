<!-- v.1.0.0_cep_KR_0706.md (updated 2026-07-06) -->

## 8. Phase 5-1 — KBF User Prompts (AI 챗봇 예시 질문 생성)

### 목적

CEP, 나노인텐트, KBF 메타데이터를 기반으로 실제 사용자가 AI 챗봇에 입력할 법한 **자연스러운 질문 9개(KBF당 3개)**를 생성합니다.

### 입력 변수

| 변수                        | 설명                                | 예시                        |
| --------------------------- | ----------------------------------- | --------------------------- |
| `{{cep}}`                   | CEP 상황 (`card.cep`)               | `아침 샤워 후 머리 빠질 때` |
| `{{nano_intent}}`           | 나노인텐트 (`card.nano_intent`)     | `탈모 초기 자가 진단`       |
| `{{kbf}}`                   | 카드의 모든 KBF를 `", "` 로 연결한 문자열 (`", ".join(kbf_list)`) | `약산성 pH 제품, 무향, 저자극` |
| `{{response_language_label}}` | 응답 언어 라벨 (locale KR/JP/US 기준, 기본값 Korean) | `Korean` / `Japanese` / `English` |

### 출력 형식

JSON 배열 (정확히 9개 — KBF당 3개 × 3 KBF)

```json
["질문1", "질문2", "질문3", "질문4", "질문5", "질문6", "질문7", "질문8", "질문9"]
```

### 요청 모델 및 파라미터

| 파라미터    | 설정값                                | 비고                                  |
| ----------- | ------------------------------------- | ------------------------------------- |
| model       | `'gpt-5.4-nano'`                      | 고정                                  |
| input       | `buildKbfUserPromptsPrompt(...)` 결과 문자열 | CEP / Nano Intent / KBF / 출력 언어 반영 |
| text        | `{ format: { type: 'text' } }`        | 일반 텍스트 출력                      |
| reasoning   | `{ effort: 'none' }`                  | 추론 effort 최소                      |

### 코드 위치

- 상수: `KBF_USER_PROMPTS_TEMPLATE`
- 코드 위치: `app/module/clients/cep_prompts.py:963`
- 호출 API: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt 템플릿

```
Using the CEP (Category Entry Point), Nano Intent, and KBFs (Key Buying Factors) below, generate natural questions that real users would ask AI chatbots (ChatGPT, Gemini, Perplexity, etc.).
Focus on high-intent, compound prompts that commonly appear in AI search.
Tone: Write in a friendly, casual, approachable style—as if chatting with a helpful friend.
Provide exactly 9 items (3 questions per KBF) as a list with no additional explanation.

Input:
- CEP (Category Entry Point): {{cep}}
- Nano Intent: {{nano_intent}}
- KBFs (Key Buying Factors): {{kbf}}

Output language: {{response_language_label}}

Response format (output ONLY a JSON array, nothing else):
["question1", "question2", ... , "question9"]
```
