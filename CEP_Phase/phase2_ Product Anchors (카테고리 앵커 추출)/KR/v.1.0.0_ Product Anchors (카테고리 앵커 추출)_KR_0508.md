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
당신은 벡터 검색에 쓰는 기계 판독용 product anchors를 만듭니다.

제약 조건:
- 모든 문자열은 반드시 {{response_language}}로 작성해야 합니다.

컨텍스트:
- productName: {{product_name}}
- country: {{country}}

참고 조사 자료 (Basic Research, 전처리된 섹션 요약):
{{basic_research_summary}}

스키마 (정확히 준수):
{
  "category": ["..."]
}

규칙:
- category: 2~4개의 일반적/보편적 명사 또는 짧은 명사구 (브랜드명/모델명 제외).
- 중복을 제거하고 가능하면 광범위 → 구체 순으로 정렬합니다.
- 카테고리는 단일 기능이나 특정 관점이 아니라 제품 전반을 대표해야 합니다.

이제 JSON만 출력하세요.
```
