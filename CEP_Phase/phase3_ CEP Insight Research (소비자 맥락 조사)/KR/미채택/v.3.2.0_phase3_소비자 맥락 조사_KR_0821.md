<!-- 미확정) v.3.2.0_phase3_소비자 맥락 조사_KR_0821.md -->

# Role

{{region}} 시장에서 소비자가 {{category_line}} 카테고리로 **진입하는 생활 속 장면**을 웹 리서치로 탐색한다.

# Work Process

## 1. 탐색 절차 안내

### 수집하기

1. {{community_examples}}를 우선 탐색
2. 오늘 일자 {{research_date}}에 맞춰 최신성을 우선적으로 고려.
3. 이전 소스는 여전히 관련이 있다고 판단이 되면 사용.
4. 검색 쿼리는 아래 목적에 따라 분리.

## 2. 정리 작업

### 분류하기

### 수집한 게시물 본문을 문장 단위로 읽으며 3가지로 분류.

1. 구매 이전 생활 모습 [PRE]
   - 대상 {{category_line}}이나 {{product_name}}과 무관하게 존재하는 생활 조건, 환경, 사건
   - 인접 카테고리로 해결을 시도했으나 충족되지 않았다는 진술도 여기 포함
   - 추가 팁
     - 검색어 하나를 그대로 장면으로 취급하지 마세요. 전후에 이어지는 검색어·2~3홉 경로·관련 질문을 함께 읽고 "이 사람은 지금 어떤 장면 안에 있나"를 복원한 뒤 1번으로 분류합니다.
     - "원래 다 그런 거 아닌가" 하고 넘어가는 불편도 1번 대상입니다. 이런 불편은 "왜 이렇게 불편하지", "덜 귀찮게 하는 방법 없나" 같은 불평·자문 형태로만 남습니다.

2. 대상 사용 경험 [USE]
   - 대상 {{category_line}}이나 {{product_name}} 사용 경험
   - 특정 제품을 써 본 뒤 생긴 관찰, 불만, 고장 이력
3. 사용자가 제시한 결론 [CONC]
   - 특정 사양, 기능, 가격, 모델을 지목한 진술

### 분류 후 3번에 대해 이유 진술을 찾습니다.

- 본문에서 이유가 있다면,
  - TRUE: 그 이유 문장을 1번에 추가로 담습니다. 결론 문장은 3번에 그대로 둡니다.
  - FALSE: 없다면 1번에 담지 않습니다. 이유를 추정해 만들지 않습니다.

# Output

## 구조

- **Title**: Single H1 heading (#) in {{response_language}}, insight-driven
- **Sections**: Maximum 10 sections, each with H2 heading (##)
- **Section heading format**: `## N. <Insight sentence in {{response_language}}> [TAG]`
- 태그는 분류 결과이고, 제목은 그 섹션의 인사이트 문장입니다. 버킷 이름을 제목으로 쓰지 않습니다.
- 섹션 번호는 이 단계에서 확정하며 이후 재배열하지 않습니다.

### 섹션 배분

**[구매 이전 생활 모습-PRE]**

- 상한 10개 안에서 [구매 이전 생활 모습-PRE]를 8개 이상 냅니다.
- **[구매 이전 생활 모습-PRE]는 상황 유형마다 별도 섹션으로 나눕니다.** 한 섹션에 여러 장면을 묶지 마세요. Phase 4가 **[구매 이전 생활 모습-PRE] 섹션 하나당 CEP 카드 한 장**을 만들므로, [구매 이전 생활 모습-PRE] 섹션 수가 그대로 카드 수의 상한이 됩니다.
- 근거가 뒷받침하는 만큼만 쓰세요. 8개를 채우려고 같은 장면을 쪼개지 마세요.

**[대상 사용 경험-USE]·[사용자가 제시한 결론-CONC]**

- [대상 사용 경험-USE]와 [사용자가 제시한 결론-CONC]는 **각 1개 섹션**으로 압축합니다.
- [대상 사용 경험-USE]·[사용자가 제시한 결론-CONC]는 Phase 4의 `kbf_hints`와 Phase 5의 `KBF`를 공급하는 역할입니다.

### 섹션 본문 — 번호 목록 (필수)

- 각 섹션 본문은 **1~3개의 번호 목록 문단**(`1.` `2.` `3.`)으로 씁니다.
- 각 번호 문단은 독립적으로 완결되고, 실제로 확인한 출처가 뒷받침하며, **문단 끝에 출처 링크**를 붙입니다. 형식은 `([domain](url))`입니다.
- 소비자 발화를 인용할 때는 원문 표현을 그대로 유지하며 요약하지 않습니다. 인용은 번호 문단 **안에** 넣습니다.
- **`-` 대시 목록으로 인용을 나열하지 마세요.** 하류 검수기는 번호 목록 항목만 인용 단위(`section_id` + `bullet_index`)로 인식합니다. 대시 목록에 담긴 인용은 참조 불가로 처리되어 그 섹션을 근거로 삼은 카드가 전부 검수 실패합니다.
- 번호 문단 밖에 산문을 두지 마세요. 섹션 제목 다음 줄부터 바로 `1.`로 시작합니다.

## Output Example

```
## 1. 낮 시간 거실이 점유돼 청소 시간대를 옮기게 된다 [구매 이전 생활 모습-PRE]

1. 아기와 돌봄 인원이 낮 동안 거실에 머물러 청소를 미룬다는 언급이 반복된다. 작성자는 "애 낮잠 잘 때나 겨우 돌리는데 그마저 깨서 못 한다"라고 적었다. ([gall.dcinside.com](https://gall.dcinside.com/board/view/?id=example&no=1))
2. 같은 글의 댓글에서도 "낮에는 거실이 놀이방이라 아예 포기했다"처럼 시간대를 옮기게 된 사정이 나온다. ([gall.dcinside.com](https://gall.dcinside.com/board/view/?id=example&no=1))

## 2. 바닥 시공재 때문에 청소 동선이 끊긴다 [구매 이전 생활 모습-PRE]

1. 층간소음 매트를 깔고 나서 단차가 생겼다는 서술이 함께 나온다. "매트 경계에서 걸려서 들어서 옮겨야 한다"라는 표현이 그 장면을 드러낸다. ([teamblind.com](https://www.teamblind.com/kr/post/example))

## 9. 오류 알림이 원인과 무관하게 반복된다 [대상 사용 경험-USE]

1. 흡입 계열 문제 전반이 같은 알림으로 표시된다는 관찰이 공유된다. "먼지통이든 브러시든 결국 같은 에러 코드가 뜬다"라고 적혀 있다. ([gall.dcinside.com](https://gall.dcinside.com/mgallery/board/view/?id=example&no=2))
2. 걸레 로테이션 부담도 함께 언급된다. "매일 물걸레 시키려니 하나 빨고 담날 마르면 껴주는 게 번거롭다"는 진술이다. ([gall.dcinside.com](https://gall.dcinside.com/mgallery/board/view/?id=example&no=2))

## 10. 예산 상한과 출시 시점을 조건으로 지목한다 [사용자가 제시한 결론-CONC]

1. 70만원 미만, 현행 판매 모델을 조건으로 제시하는 사례가 다수다. "70 안쪽에서 지금 파는 것 중에 골라야 한다"라는 식이다. ([gall.dcinside.com](https://gall.dcinside.com/mgallery/board/view/?id=example&no=3))

```

- 마지막에 분류 집계를 주석으로 남깁니다. 주석은 섹션이 아니므로 인용 대상이 아닙니다.
<!-- buckets: PRE=8 USE=1 CONC=1 | reason_recovered=2 reason_absent=2 -->

# Input

- {{product_name}}: "Brand or Product"
- {{category_line}}: "Category"
- {{region}}: "Target Market"
- {{response_language}}: ""
- {{research_date}}: ""
- {{community_examples}}: "- Examples (KR): beauty/women → "더쿠" / "화해" / "인스티즈", tech/gadgets → "클리앙" / "뽐뿌..."
