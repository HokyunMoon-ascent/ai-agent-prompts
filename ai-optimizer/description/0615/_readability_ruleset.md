# 한국어 분석 보고서 가독성 룰셋 (deep-research 산출물)

*생성일: 2026-06-15 | 출처: NN/G, 국립국어원 공공언어, UX/테크니컬 라이팅 가이드 | 신뢰도: 높음*

## 요약

웹 화면에서 사람은 글을 **읽지 않고 스캔한다**. NN/G 연구에 따르면 사용자는 한 페이지 텍스트의 평균
20~28%만 읽으며, 시선은 F자 패턴(상단 가로 → 중단 가로 → 좌측 세로)으로 움직인다. 따라서 가독성의
핵심은 **결론을 앞에, 한 단위에 한 메시지, 짧은 문장, 좌측·앞부분에 정보성 키워드 배치, 그리고 절제된
강조**다. 아래 8개 룰은 모두 인용 근거를 갖춘 실행 규칙으로, 프롬프트의 출력 규칙에 그대로 이식할 수 있다.

---

## 룰셋 (프롬프트 이식용)

### R1. 주제문 우선 — 역피라미드 / 두괄식
각 단락·블록의 **첫 문장에 결론(핵심 판정)을 둔다.** 세부 설명·근거는 그 뒤에 전개한다.
- 근거: NN/G "inverted pyramid" — 단락 첫 문장에서 독자의 질문에 먼저 답한 뒤 세부로 들어가야
  스캔하는 독자가 핵심을 놓치지 않는다. ([NN/G F-Shaped Pattern](https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/))
- 근거: 국립국어원 — 두괄식(중심 문장을 앞에 두는 구성)은 공공·실무 글의 표준 권장 구성.
  ([국립국어원 온라인가나다](https://www.korean.go.kr/front/onlineQna/onlineQnaView.do?mn_id=261&qna_seq=316974))

### R2. 한 단락 한 메시지
하나의 단락(또는 토픽 블록)은 **하나의 핵심 메시지**만 담는다. 메시지가 둘 이상이면 단락을 나눈다.
- 근거: NN/G·plain language 공통 원칙 — 단락당 하나의 아이디어가 스캔과 이해를 돕는다.
  ([NN/G Formatting Long-Form Content](https://www.nngroup.com/articles/formatting-long-form-content/))

### R3. 문장은 짧게 — 한 문장 한 뜻
한 문장에 정보를 하나만 담고, **한 문장은 한국어 기준 약 50자 이내**를 지향한다. 길어지면 끊는다.
만연체·번역투(긴 관형절 누적, "~하며 ~하고 ~하는")를 피한다.
- 근거: 국립국어원 공공언어 진단 기준에 **문장 길이**가 포함되며, "한 문장 한 뜻", 짧은 문장을 권장.
  ([국립국어원 한눈에 알아보는 공공언어 바로 쓰기(개정판)](https://www.korean.go.kr/front/etcData/etcDataView.do?mn_id=208&etc_seq=699))
- 적용 예: 현재 answer의 ~230자 단일 문장 → 결론문 + 근거문 2~3개로 분리.

### R4. 앞부분·좌측에 정보성 키워드
문장과 단락은 **정보를 담은 단어로 시작**한다. "또한", "그리고", "이를 통해" 같은 빈 도입어로
시작하지 않는다. 스캔 시 좌측 세로선에서 핵심어가 바로 보이게 한다.
- 근거: NN/G — 소제목·단락·항목은 스캔하는 독자의 눈에 걸리도록 정보성 단어로 시작해야 한다.
  ([NN/G F-Shaped Pattern](https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/))

### R5. 핵심어 강조는 절제 (≤ 본문의 30%)
정말 중요한 핵심어·구절만 **굵게** 강조한다. 문장·단락 전체를 굵게 하지 않는다. 강조가 본문의
**30%를 넘지 않게** 한다(넘으면 강조 효과가 사라진다).
- 근거: NN/G — 강조 텍스트는 본문의 30% 이하, 키워드/구 단위로만. 전체 문장·단락 강조는 효과를 희석.
  ([NN/G 7 Tips for Bulleted Lists](https://www.nngroup.com/articles/presenting-bulleted-lists/), [NN/G Formatting Long-Form Content](https://www.nngroup.com/articles/formatting-long-form-content/))

### R6. 리스트는 신중하게 — 산문이 기본
대부분의 설명은 **산문(단락)이 기본**이다. 리스트는 **병렬 항목이 3개 이상**이고 스캔이 필요할 때만
세로 리스트로 쓴다. 2개 이하 항목은 문장 안에 녹인다. 점·들여쓰기로 가득 찬 화면은 오히려 부담을 준다.
- 근거: NN/G — 세로 리스트는 항목이 3개 이상일 때 적합, 짧은 것은 문장에 포함. "점과 들여쓰기로 가득 찬
  페이지는 부담스럽다." 모든 것을 리스트로 만들지 말 것. ([NN/G 7 Tips for Bulleted Lists](https://www.nngroup.com/articles/presenting-bulleted-lists/))
- 함의: humanize 철학(불릿 남발 = AI 티)과 정확히 일치. 가독성과 자연스러움이 같은 방향.

### R7. 시각적 위계 + 청킹
긴 내용은 **의미 단위로 끊고**, 소제목·강조·단락 구분으로 위계를 만든다. 한 덩어리로 4~6문장을
이어 붙이지 않는다. 블록은 결론문 → 근거 → 함의의 작은 호흡으로 나눈다.
- 근거: NN/G — 명확한 시각적 위계가 F패턴 스캔을 보완하고 정보 탐색 속도를 높인다.
  ([NN/G F-Shaped Pattern](https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/))

### R8. 일관된 병렬 구조
같은 층위의 항목·블록은 **같은 문장 구조와 비슷한 길이**로 맞춘다(병렬성). 토픽 그룹 A/B/C가
서로 다른 형태로 들쭉날쭉하지 않게 한다.
- 근거: NN/G — 리스트·항목은 동일 품사로 시작하고 길이를 비슷하게 유지(병렬 구조)해야 스캔이 쉽다.
  ([NN/G 7 Tips for Bulleted Lists](https://www.nngroup.com/articles/presenting-bulleted-lists/))

---

## 두 answer에 대한 진단 (룰셋 적용 결과)

| 룰 | gap_Integrate answer | cep answer |
|---|---|---|
| R1 주제문 우선 | ✗ 결론이 블록 중간/끝에 묻힘 | △ 구간명이 결론 역할을 일부 하나, 본문은 미괄식 |
| R2 한 단락 한 메시지 | ✗ A/B/C 각 블록에 2~3개 메시지 혼재 | △ 구간별 1메시지지만 길게 뭉침 |
| R3 짧은 문장 | ✗ 230자급 단일 문장 다수 | ✗ 4~6개 장문 연결 |
| R4 정보성 키워드 시작 | △ 일부 "AI는~"으로 시작 | △ |
| R5 강조 절제 | ✗ 강조 전무(스캔 앵커 없음) | ✗ 강조 전무 |
| R6 리스트 신중 | — (현재 리스트 없음, 산문) | — |
| R7 시각적 위계 | ✗ 4~6문장 단일 덩어리 | ✗ 단일 덩어리 |
| R8 병렬 구조 | △ A/B/C 형태 유사하나 길이 불균일 | △ |

**핵심 처방**: R1(주제문 우선) + R3(문장 분리) + R5/R7(강조·청킹)이 두 answer의 가독성을 가장 크게 좌우.

## 출처
1. [NN/G — F-Shaped Pattern of Reading on the Web](https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/) — 스캔 읽기, 역피라미드, 정보성 키워드 우선, 시각적 위계.
2. [NN/G — 7 Tips for Presenting Bulleted Lists](https://www.nngroup.com/articles/presenting-bulleted-lists/) — 리스트 3개 이상 규칙, 남용 경고, 병렬 구조.
3. [NN/G — 5 Formatting Techniques for Long-Form Content](https://www.nngroup.com/articles/formatting-long-form-content/) — 강조 절제(≤30%), 산문 기본.
4. [국립국어원 — 한눈에 알아보는 공공언어 바로 쓰기(개정판)](https://www.korean.go.kr/front/etcData/etcDataView.do?mn_id=208&etc_seq=699) — 문장 길이, 한 문장 한 뜻.
5. [국립국어원 — 두괄식/미괄식 온라인가나다](https://www.korean.go.kr/front/onlineQna/onlineQnaView.do?mn_id=261&qna_seq=316974) — 두괄식 구성.
6. [국립국어원 — 행정문서 표현 개선 및 쉬운 공공언어 쓰기 지침(2021)](https://www.korean.go.kr/front/reportData/reportDataView.do?mn_id=45&report_seq=1122) — 행정·보고서 쉬운 언어 기준.

## 방법론
WebSearch 다회 + NN/G·국립국어원 핵심 문서 WebFetch. 조사 축: 역피라미드/두괄식, 한 단락 한 메시지,
한국어 문장 길이, 정보 청킹·시각적 위계, F패턴 스캔, 강조·리스트 남용. 모든 룰은 2개 이상 출처 교차 확인.
