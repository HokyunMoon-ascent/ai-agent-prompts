<!-- v.1.0.0_cep_KR_0706.md (updated 2026-07-06) -->

## 8. Phase 5-1 — KBF User Prompts (AI 챗봇 예시 질문 생성)

### 목적

CEP, KBF 메타데이터를 기반으로 실제 사용자가 AI 챗봇에 입력할 법한 **자연스러운 질문 9개(KBF당 3개)**를 생성합니다.

> 할루시네이션 억제 개정(0714): 나노인텐트가 Phase 4 출력에서 제거됨에 따라 입력 변수 `{{nano_intent}}`를 제거하고 CEP + KBF 기반으로 재구성했습니다.
> ⚠ 백엔드 정합 필요: `buildKbfUserPromptsPrompt(...)`의 nano_intent 인자 제거.

### 입력 변수

| 변수                          | 설명                                                              | 예시                              |
| ----------------------------- | ----------------------------------------------------------------- | --------------------------------- |
| `{{cep}}`                     | CEP 상황 (`card.cep`)                                             | `아침 샤워 후 머리 빠질 때`       |
| `{{kbf}}`                     | 카드의 모든 KBF를 `", "` 로 연결한 문자열 (`", ".join(kbf_list)`) | `약산성 pH 제품, 무향, 저자극`    |
| `{{response_language_label}}` | 응답 언어 라벨 (locale KR/JP/US 기준, 기본값 Korean)              | `Korean` / `Japanese` / `English` |

### 출력 형식

JSON 배열 (정확히 9개 — KBF당 3개 × 3 KBF)

```json
[
  "질문1",
  "질문2",
  "질문3",
  "질문4",
  "질문5",
  "질문6",
  "질문7",
  "질문8",
  "질문9"
]
```

### 요청 모델 및 파라미터

| 파라미터  | 설정값                                       | 비고                                     |
| --------- | -------------------------------------------- | ---------------------------------------- |
| model     | `'gpt-5.4-nano'`                             | 고정                                     |
| input     | `buildKbfUserPromptsPrompt(...)` 결과 문자열 | CEP / KBF / 출력 언어 반영               |
| text      | `{ format: { type: 'text' } }`               | 일반 텍스트 출력                         |
| reasoning | `{ effort: 'none' }`                         | 추론 effort 최소                         |

### 코드 위치

- 상수: `KBF_USER_PROMPTS_TEMPLATE`
- 코드 위치: `app/module/clients/cep_prompts.py:963`
- 호출 API: `POST create_kbf_user_prompts` · `cep_finder.py:683`

---

### Prompt 템플릿

```
아래의 CEP (Category Entry Point), KBF (Key Buying Factors)를 활용해 실제 사용자가 AI 챗봇(ChatGPT, Gemini, Perplexity 등)에 물어볼 법한 자연스러운 질문을 생성하세요.
AI 검색에서 흔히 나타나는, 구매 의도가 높은 복합형 프롬프트에 집중하세요.
톤: 도움을 주는 친구와 대화하듯 친근하고 편안하며 다가가기 쉬운 스타일로 작성하세요.
정확히 9개 항목(KBF당 질문 3개)을 추가 설명 없이 목록 형태로 제공하세요.

Input:
- CEP (Category Entry Point): {{cep}}
- KBFs (Key Buying Factors): {{kbf}}

Output language: {{response_language_label}}

응답 형식(오직 JSON 배열만 출력하고 그 외에는 아무것도 출력하지 마세요):
["질문1", "질문2", "질문3", "질문4", "질문5", "질문6", "질문7", "질문8", "질문9"]
```
