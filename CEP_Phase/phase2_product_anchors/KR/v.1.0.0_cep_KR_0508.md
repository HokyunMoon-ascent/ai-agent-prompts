<!-- v.1.0.0_cep_KR_0508.md (updated 2026-05-08) -->

## Phase 2 — Product Anchors (카테고리 앵커 추출)

### 목적

기본 정보 사전 조사 결과를 바탕으로 **벡터 검색용 카테고리 키워드**를 추출합니다.  
이후 단계의 키워드 검색에서 제품 카테고리 맥락으로 활용됩니다.

### 입력 변수

| 변수                         | 설명                          | 예시              |
| ---------------------------- | ----------------------------- | ----------------- |
| `{{product_name}}`           | 제품명                        | `갤럭시 S25`      |
| `{{country}}`                | 국가 코드                     | `kr`              |
| `{{response_language}}`      | 출력 언어 (ko/en/ja)          | `ko`              |
| `{{basic_research_summary}}` | 기본 정보 사전 조사 결과 요약 | (마크다운 텍스트) |

### 출력 형식

JSON 객체

```json
{
  "category": ["스마트폰", "안드로이드폰", "플래그십폰"]
}
```

### 요청 모델 및 파라미터

| 파라미터  | 설정값                                                                                      |
| :-------- | :------------------------------------------------------------------------------------------ |
| model     | 'gpt-5.4-nano'                                                                              |
| input     | buildProductAnchorsPrompt({ productName, country, responseLanguage, basicResearchSummary }) |
| text      | { format: { type: 'text' } }                                                                |
| reasoning | { effort: 'none' }                                                                          |

---

### Prompt 템플릿

```
You are generating machine-readable product anchors for vector retrieval.

Constraints:
- All strings MUST be written in {{response_language}}.

Context:
- productName: {{product_name}}
- country: {{country}}

Visible research (Basic Research, preprocessed section summary):
{{basic_research_summary}}

Schema (exact):
{
  "category": ["..."]
}

Rules:
- category: 2-4 generic/common nouns or short noun phrases (no brand/model names).
- Deduplicate and sort from broad → specific when possible.
- The categories must represent the overall product, not a single feature or a specific perspective.

Now output ONLY the JSON.
```
