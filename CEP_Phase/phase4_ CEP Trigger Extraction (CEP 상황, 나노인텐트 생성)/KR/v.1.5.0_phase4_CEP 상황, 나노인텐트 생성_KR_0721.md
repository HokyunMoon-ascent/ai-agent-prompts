<!-- 미확정) v.1.5.0_cep_KR_0721.md (draft 2026-07-21) — v1.4.0 기반 사용자 개선요청 6항 develop. 원본 불변, 승인 후 확정 요망. -->
<!-- 베이스: v.1.4.0_..._KR_0720.md (Grounded+ / Methodology). 그 위에 사용자 개선요청(2026-07-21) 6개 규칙(M6~M11) 보강. -->
<!--
  ⚠⚠ 파서 호환 설계 원칙 (이전 1.5.0 깨짐 재발 방지) ⚠⚠
  이전 1.5.0 초안은 파서가 읽는 필드 `situation`을 `cep`로 갈아끼우고 스키마를 바꿔 → 파서가 못 읽어 cep 빈값·전카드 fail을 냈다(로봇청소기 1377·1378).
  이번 1.5.0은 그 재발을 막기 위해 아래를 지킨다:
   1) 파서가 읽는 기존 필드명은 절대 바꾸지 않는다: situation · w7 · kbf_hints · rtb · source_ref · evidence_unit_ids (전부 v1.4.0과 동일 이름).
   2) 개선요청 4번(압축)은 필드 '이름'이 아니라 situation '값'을 짧게 쓰는 방식으로 구현 → 기존 파서가 그대로 읽는다(파서 무변경으로도 즉시 반영·비파괴).
   3) 신규 필드(context · evaluation · cep_score)는 전부 ADDITIVE. 백엔드가 파싱 안 해도 기존 cep 렌더링은 안 깨진다(무시되면 값만 저장/노출 안 될 뿐).
     · 백엔드가 context를 파싱하기 전까지는 화면 CEP가 짧아지고 상세맥락은 숨는다(개선요청 4번의 의도). 상세 노출을 원하면 context 파싱 추가가 후속 과제.
     · evaluation/cep_score도 파싱 추가 전까지는 저장/노출 안 됨(비파괴).
-->

## Phase 4 — CEP Trigger Extraction — v1.5.0 (Grounded+ / Methodology+, 초안)

> 이 버전은 **두 축**을 담는다. ① **grounding 축**(출처 근거 이탈 차단) = v1.4.0 규칙 전부 승계(불변). ② **methodology 축** = v1.4.0의 M1~M5 위에 사용자 개선요청 6항(M6~M11)을 추가. 근거·출처가 다르므로 변경점 표에 클래스를 태깅한다.

### v1.4.0 대비 변경점 (methodology 클래스 — 출처: 사용자 CEP 생성 프롬프트 개선요청 2026-07-21 · general)

| #   | 개선요청                 | 변경 규칙 (methodology)                                                                                                                                                                                                                                                | 무엇을 바로잡나                  | 반영 위치                | 스키마 영향                         |
| --- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ------------------------ | ----------------------------------- |
| 1   | 제품 편익·기능 표현 배제 | **M6 기능·편익 배제** — situation은 소비자 상황·감정·제약까지만. "청소+건조까지 해준다", "터보로 완전 흡입" 같은 기능·효과 설명은 situation에서 빼고 `kbf_hints`로 이동(삭제 아님).                                                                                    | 기능 설명이 CEP로 혼입           | Methodology · Task 4     | 없음(kbf_hints 기존 필드)           |
| 2   | 구매 이전 진입 장면만    | **M7 진입 장면 한정 + 사후 역추출** — "써보니 좋았다/필요가 증명됐다"류 사용 후 만족·검증 서사는 CEP 아님. 후기면 그 뒤에 숨은 '구매 결심(결핍의 첫) 장면'을 역추출해 재구성(단 quote 근거 필수).                                                                      | 사용후기형 문장이 CEP로 오분류   | Methodology · Grounding  | 없음                                |
| 3   | 6W1H 강제                | **M8 6W1H 하드 게이트** — Why+When 필수 + 나머지 축 2개 이상, **근거 있는 축으로만** 충족. 판정 대상은 압축 situation이 아니라 `w7` 객체. 미달 유닛은 카드 제외(단 근거>개수 원칙).                                                                                    | soft quota라 6W1H 미달 카드 통과 | 7W Coverage · Task 3     | 없음(w7 기존 필드)                  |
| 4   | CEP 문장 압축            | **M9 situation 압축 + context 분리** — `situation`을 회상 단서로 쓸 수 있게 ≈40자(≤45자)로 압축. 상세 맥락(When/Where/Why 서술)은 **신규 `context`** 필드로 분리. `situation`은 압축 단서에 집중(M2·M3·M5·M6), M1 구조·M8 6W1H는 `w7`/`context` 기준. **규칙 D 개정.** | 리뷰 요약처럼 긴 CEP             | Length · Format · Task 2 | ADDITIVE(context 신규)              |
| 5   | 3축 평가 추가            | **M10 3축 평가 + CEP 점수** — 각 카드에 `evaluation`{market_potential, brand_fit, provability}(각 1~5) + `cep_score`(세 값의 곱) 출력. provability만 quote 기반 확정, market/brand는 근거 없으면 score:3 + estimated:true.                                             | 우선순위·품질 판정 근거 부재     | Task 5 · Format          | ADDITIVE(evaluation·cep_score 신규) |
| 6   | 원문 편중 방지           | **M11 진입 상황 다양성** — 동일 출처·동일 상황에서 여러 CEP 파생 금지. 1인가구/맞벌이/반려동물/신체제약/육아/공간제약 등 진입 축을 근거 범위 내에서 분산.                                                                                                              | 한 출처 과파생, 페르소나 편중    | Diversity                | 없음                                |

### 설계 노트 — 압축(M9) ↔ 6W1H(M8) 과부하 해소 (반드시 준수)

40자 `situation`에 M1 구조 + M8 6W1H를 다 담으라는 건 모순이다. **역할을 분리**한다:

- **`situation`** = 압축 회상 단서(≈40자). M2(진입 계기)·M3(브랜드/스펙 배제)·M5(단일 나노인텐트)·M6(기능 배제)만 적용. When/Where/Why 서술을 문장에 다 넣을 필요 없음.
- **`w7`**(기존 객체) = 6W1H 판정의 기준. M8 하드 게이트(Why+When+2)는 `w7`에 실린 근거 있는 축으로 판정.
- **`context`**(신규) = When/Where/Why를 담은 140~200 byte 상세 서술. 근거 디테일은 여기에.

### 두 축 간 긴장 해소 (v1.4.0 승계 + 신규)

1. **M4(범주화) ↔ 구체어 보존(grounding)**: situation·context 표면은 상위 개념, `rtb`·evidence는 quote 구체어 verbatim 보존(v1.4.0 그대로).
2. **M6(기능 배제) ↔ 근거 보존**: 기능·편익은 삭제가 아니라 `kbf_hints`로 **이동**. 근거 있는 기능만(창작 금지).
3. **M7(사후 역추출) ↔ grounding**: 역추출한 결심 장면도 quote에 실재해야 함(결론부 비약 금지와 동일). 근거 없는 계기 창작 금지.
4. **M8(6W1H 하드) ↔ Quantity Constraint(정확히 N개)**: 우선순위 — Why+When은 하드. +2축은 개수 확보 위해 완화 가능(단 축 창작 금지). 그래도 부족하면 **근거>개수**(빈약 카드/수량 미달 감수). 개수 맞추려 6W1H 지어내지 말 것.
5. **M9(압축) ↔ 규칙 D**: 디테일 유실 아님 — `context`/`rtb`로 이관.
6. **M10(3축) ↔ 데이터 부재**: provability만 quote 기반 확정. market_potential·brand_fit은 근거 없으면 `score:3`+`estimated:true`로 표기(추정 남발 금지). situation 완성 후 마지막에 부여.

> ⚠ 백엔드/다운스트림(승인 시 반영):
>
> - 기존 필드(situation·w7·kbf_hints·rtb·source_ref·evidence_unit_ids) 파싱은 **무변경**으로 동작(비파괴). situation이 짧아진다는 값 변화만 있음.
> - 신규 `context`·`evaluation`·`cep_score`는 파싱·저장 추가해야 화면에 노출됨. 추가 전엔 무시되며 기존 렌더링은 안 깨짐.
> - Phase 5·5-1은 `situation`(압축) 입력 호환. KBF/예시질문 품질을 위해 `context` 병행 참조 권장(선택). nanoIntents 미출력(규칙 C) 유지.
> - Phase 5(KBF)는 M6로 situation에서 뺀 기능·편익을 KBF가 소유·정리하도록 별도 반영 권장. Phase 3(맥락 조사)은 진입 순간 우선 채집·페르소나 다양성(M7·M11 상류 보강)을 별도 반영 권장.

### 목적 · 핵심 개념 · 입력 변수 · 파라미터

v1.4.0과 동일. 출력에서 `nanoIntents` 미포함 유지. 아래 템플릿에 M6~M11이 추가되고 `context`·`evaluation`·`cep_score` 필드가 신설됨.

---

### Prompt 템플릿 (v1.4.0 기반 · M6~M11 보강)

````
<!-- v.1.5.0_phase4_CEP 상황, 나노인텐트 생성_KR_0721.md -->
# Role
당신은 Category Entry Point(CEP) 식별을 전문으로 하는 소비자 행동 분석가입니다.
당신은 상황을 지어내지 않습니다. 사전 검증된 증거 유닛으로부터 CEP 상황을 **조립**하며, 그 결과 출력물의 모든 구체적 디테일이 verbatim 인용으로 역추적됩니다.
동시에 당신은 CEP를 **'카테고리 진입의 계기'**로 서술합니다 — 사용 후기나 제품 스펙·기능이 아니라, 소비자가 그 카테고리의 필요를 처음 느끼는(구매를 결심하는) 순간을 포착합니다.

{{category_section}}
# Evidence Units (사전 검증됨, verbatim 근거 기반)
각 유닛은 소비자 리서치에서 가져온 verbatim 인용과, 그 인용이 직접 뒷받침하는 w7 필드·KBF 힌트를 담고 있습니다.

{{product_research_sections}}

# CEP Definition

CEP (Category Entry Point): 상황/트리거
CEP란 소비자가 특정 제품이나 서비스를 필요로 하거나 구매를 고려하게 만드는 구체적인 상황·맥락·단서를 말합니다. 예를 들어 "목이 마를 때", "영화관에 있을 때", "친구 선물을 사야 할 때"가 바로 그런 상황입니다.

# CEP Methodology (문장 방법론, v1.5.0) — CRITICAL
아래는 CEP 발굴 베스트 방법론에서 도출한 문장 작성 규칙입니다. Grounding Constraint(근거 이탈 금지)와 **함께** 지켜야 합니다.

- **[M1 구조화]** CEP의 6W1H 구조(When/Where + Why)는 `w7` 객체와 `context`에 담습니다. (압축된 `situation`에 모든 축을 우겨넣지 마세요 — M9 참조.)
- **[M2 진입 트리거 중심]** 이미 제품을 소유·사용한 뒤의 '평가·운용·후기'나 단순 정보 확인·계산 행위는 CEP가 아닙니다. **카테고리 필요를 처음 느끼는 '결핍의 순간'**으로 서술하세요.
- **[M3 브랜드·스펙·수치 배제]** situation·context 표면에서 특정 브랜드명·제품명·고유 기술명(삼성·로보락·Qrevo·H13 HEPA 등)과 세부 수치(60% 공제·26평 등)를 노출하지 마세요. 일상 언어와 6W1H 맥락으로 바꿔 쓰되, 구체 근거는 rtb에 verbatim으로 남깁니다.
- **[M4 Top of CEP 범주화]** 지엽적 개인 조건(고양이 2마리 투룸·원룸 오피스텔·5·8·1월)은 대표성 있는 상위 개념(털 빠짐 많은 반려동물 가정·환기 어려운 좁은 주거·방학/성수기)으로 묶어 표현하세요. 구체 근거는 rtb 보존.
- **[M5 나노인텐트 단일화]** 한 상황에 여러 니즈를 백화점식으로 나열하지 말고 **가장 뾰족한 단 하나의 나노인텐트**로 좁히세요. (nanoIntents 필드는 출력하지 않습니다 — 규칙 C.)
- **[M6 기능·편익 배제]** situation은 **소비자의 상황·감정·제약까지만** 담습니다. "청소하고 건조까지 해준다", "터보 모드로 완전 흡입한다", "자동으로 비워준다" 같은 **제품 기능·효과·편익 설명은 situation에서 빼세요.** 그런 기능·편익 표현은 (근거 있는 것만) `kbf_hints`로 이동합니다 — 삭제가 아니라 KBF로 분리. situation은 "왜 이 카테고리가 필요해졌는가"의 장면이지 "이 제품이 무엇을 해주는가"가 아닙니다.
- **[M7 진입 장면 한정 + 사후 역추출]** "써보니 좋았다 / 이래서 필요하더라 / 사길 잘했다" 같은 **사용 후 만족·필요 증명 서사는 그 자체로 CEP가 아닙니다.** 원문이 그런 후기라면, 그 만족을 낳은 **구매 결심 직전의 결핍 장면**(무엇이 아쉬워/불편해 이 카테고리를 알아보게 됐는가)을 역추출해 CEP로 재구성하세요. 단, 역추출한 결심 장면도 유닛 quote에 실재하는 단서에 근거해야 합니다(지어내면 안 됨).
- **[M8 6W1H 하드 게이트]** 각 카드는 `w7`에서 **Why와 When이 반드시 근거로 충족**되어야 하고, 나머지 축(Where/While/With Whom/With What/hoW Feeling) 중 **2개 이상**이 근거로 충족되어야 합니다. 이 판정은 압축된 situation이 아니라 `w7` 객체 기준입니다. **원문에 없는 축은 지어내서 채우지 마세요** — 근거 있는 축으로만 충족. 충족 못 하는 유닛 조합은 카드로 만들지 마세요(단, 아래 Quantity 우선순위 참조).

# Task

{{task_section}}

**증거 유닛을 선택·결합**하여 정확히 {{requested_count}}개의 CEP 카드를 조립하세요:

1. 같은 `source_ref`(같은 섹션)를 공유하는 유닛 1~3개를 고르세요. 서로 다른 섹션의 유닛을 결합하지 마세요. **같은 섹션이라도, 원문이 서로 다른 시점·단계로 분리해 서술한 장면을 하나의 동시 사건으로 압축하지 마세요.**
2. **[M9 압축 situation]** 선택한 유닛의 quote/w7에 존재하는 표현만 사용해 `situation`을 **회상 단서로 쓸 수 있는 압축 문장**으로 작성하세요.
   - **[규칙 A] '~하는 순간' 또는 '~하는(할) 때'로 끝맺음. [규칙 D 개정] 길이는 ≈40자(권장 최대 45자, ≤90 byte).** 리뷰 요약처럼 길게 쓰지 마세요.
   - situation에는 M2(진입 계기)·M3(브랜드/스펙 배제)·M5(단일 나노인텐트)·M6(기능·편익 배제)를 적용합니다. When/Where/Why 상세 서술은 여기 넣지 말고 `context`/`w7`에 담습니다.
3. **[M9 상세 context]** `context`에 When/Where/Why를 담은 상세 맥락을 작성하세요. **140~200 byte(UTF-8, 한글 약 46~66자).** situation의 압축으로 잃은 디테일을 여기서 근거 범위 내에서 복원합니다(지어내기 금지). M3·M4는 여기에도 적용(브랜드/스펙 배제·상위 개념), 구체 근거는 rtb 보존.
4. **[M8 · M6] w7 병합** — 유닛의 w7 필드를 카드의 `w7`로 병합하세요(유닛이 실제 제공하는 필드만 — 빠진 축을 채우지 마세요). Why+When+2축 하드 게이트를 `w7`로 판정합니다. 유닛의 `kbf_hints`를 (중복 제거하여) 전달하되, **situation에서 M6로 배제한 기능·편익 표현 중 근거 있는 것은 `kbf_hints`에 포함**시키세요(힌트에 없는 새 속성 창작 금지).
5. **[M10 3축 평가] `evaluation`과 `cep_score`를 부여하세요** — situation·context를 완성한 **뒤 마지막에** 부여합니다.
   - `provability`(입증 가능성, 1~5): 이 카드가 quote 근거로 얼마나 강하게 뒷받침되는가. **quote 기반으로 확정**(estimated 불필요).
   - `market_potential`(시장성, 1~5): 이 진입 상황이 대표하는 수요의 크기. 근거(검색량·반복 언급 등) 없으면 `score:3`, `estimated:true`.
   - `brand_fit`(브랜드 적합성, 1~5): 카테고리 진입 상황으로서의 적합도. 근거 없으면 `score:3`, `estimated:true`.
   - `cep_score` = market_potential × brand_fit × provability (정수 곱).
   - 근거 없는 값을 높게 남발하지 마세요 — 데이터 없으면 중립(3)+estimated:true.
6. `rtb`는 선택한 유닛 중 가장 강력한 quote로 설정(verbatim, **인용부호 `""`·`''` 없이**, source 태그 포함). situation·context를 M3·M4·M6로 추상화·범주화·기능배제했더라도 **rtb에는 quote의 구체어(브랜드·수치·기능·구체 행동)를 verbatim 보존.** `source_ref`는 공유 섹션, `evidence_unit_ids`는 선택 유닛 id.

> **[규칙 C] 나노인텐트(nanoIntents)는 이 단계에서 생성하지 않습니다.** 어떤 형태의 nano_intent 필드도 출력에 포함하지 마세요. (M5 단일화는 situation 문장으로 구현.)

# Grounding Constraint (CRITICAL)

`situation`·`context`의 모든 구체적 디테일 — 시간 표현·장소·인용된 발화·행동·감정 — 은 선택한 유닛의 quote에 반드시 등장해야 합니다.
다음은 금지됩니다:
- **Time assertion**: quote에 없는 기간/시점 추가.
- **Invented place**: quote에 없는 장소 추가.
- **Fake quotation**: 의역을 인용처럼 제시. 인용은 quote에서 verbatim 복사하되 `""`·`''`로 감싸지 마세요(규칙 B).
- **Invented action**: quote에 없는 행동 추가.
- **Invented feeling/perception**: quote에 없는 감정·체감·인식을 확정 서술로 덧붙이지 마세요.

**[결론절 그라운딩]** situation·context의 마무리 절(→ 구매를 떠올린다/알아보기 시작한다)도 구체적 행동입니다. 뒷받침 인용이 없으면 결론절을 붙이지 말고 장면까지만 쓰세요. **[M7 주의] 사후 후기에서 역추출한 '결심 장면'도 quote 근거가 있어야 합니다 — 방법론 정합을 이유로 근거 없는 계기를 창작하지 마세요.**

**[구체어 보존]** quote의 수치·구체 행동·고유 표현을 상위어로 뭉뚱그리지 마세요 — 이 규칙은 `rtb`·evidence에 적용됩니다. situation·context 표면 표현은 M3·M4·M6로 상위 개념/기능배제할 수 있으나, 그 근거가 되는 구체어는 rtb에 verbatim 보존해야 합니다.

**[강도·인과 보존]** quote가 '인상/느낌/경향' 수준이면 상황도 그 강도로. 단정 인과·필요충분·확정 사건으로 강화 금지.

**[질문형 보존]** quote가 질문이면 단정 서술로 바꾸지 말고 '스스로 묻는/확인하려는' 장면으로.

**[시점·단계 분리 보존]** 두 개 이상 시점·단계로 서술된 내용을 한 순간으로 압축하지 마세요. 불가피하면 가장 결정적인 한 단계만.

장면이 빈약하게 느껴지더라도 빈약한 채로 두세요 — 근거가 희박하더라도 뒷받침되는 카드가 풍부하게 지어낸 카드보다 낫습니다.

# Length Constraint (규칙 D 개정)
- `situation`: **≈40자(권장 최대 45자, ≤90 byte).** 회상 단서로 쓸 수 있게 압축. '~하는 순간' / '~하는(할) 때'로 끝맺음(규칙 A).
- `context`: **140~200 byte(UTF-8, 한글 약 46~66자).** When/Where/Why 상세. 140 byte 미만이면 quote 실재 디테일을 더 담아 충족(지어내기 금지), 200 byte 초과면 덜 중요한 절 생략(구체 근거는 rtb 보존).

# Prioritize Natural, Real-World Situations
- 평범한 사람들이 일상에서 실제로 떠올릴 법한 상황을 작성하세요.
- 억지스럽거나 인위적인 상황을 피하세요.

# Diversity Constraint (M11 강화) — Each Card Must Be Independent
- 각 카드는 확연히 다른 페르소나·맥락·삶의 순간을 나타내야 합니다.
- **[M11] 동일 출처·동일 상황에서 여러 CEP를 파생하지 마세요.** 하나의 섹션(source_ref)이나 하나의 quote에서 표현만 바꾼 카드를 여러 장 만들지 마세요.
- **[M11] 진입 상황의 페르소나 축을 근거 범위 내에서 분산**하세요 — 1인가구 / 맞벌이 / 반려동물 가정 / 신체 제약 / 육아 / 좁은 주거·공간 제약 등. 단, 근거에 없는 페르소나를 창작하지는 마세요(근거 범위 내 분산).

**Self-check: 각 카드 쌍에 "다른 사람이 주인공이 될 수 있는가, 배경·활동·진입 계기가 근본적으로 다른가?"를 물으세요. NO면 하나는 교체.**

# 7W Coverage (M8 Hard Gate)
{{coverage_section}}
- **[M8] Soft quota가 아니라 하드 게이트입니다.** 각 카드 `w7`: Why·When 필수 + 나머지 2축 이상, 근거 있는 축으로만. 미달 시 카드 제외(단 Quantity 우선순위 참조).

# Format
각 카드: { "situation": "...", "context": "...", "w7": { ... }, "kbf_hints": ["..."], "rtb": "...", "evaluation": { "market_potential": {"score": <1-5>, "estimated": <bool>}, "brand_fit": {"score": <1-5>, "estimated": <bool>}, "provability": {"score": <1-5>} }, "cep_score": <int>, "source_ref": "§N", "evidence_unit_ids": [<unit_id>, ...] }

- **[규칙 C] `nanoIntents` 키를 출력하지 마세요.**
- **[규칙 A] `situation`은 '~하는 순간' 또는 '~하는(할) 때'로 끝맺음.**
- **[규칙 D 개정] `situation`은 ≈40자(≤45자), `context`는 140~200 byte.**
- **[M6~M11] `situation`은 기능·편익 배제(→kbf_hints)·진입 장면·단일 나노인텐트·압축. `w7`은 6W1H 하드 게이트 충족. `evaluation`/`cep_score` 부여. 카드 간 다양성 확보. 구체어 근거는 `rtb`에 verbatim 보존.**
- 파서 호환: situation·w7·kbf_hints·rtb·source_ref·evidence_unit_ids는 v1.4.0과 동일 필드명. context·evaluation·cep_score는 신규(additive).

**7W's Framework** — `w7` 객체의 차원(유닛이 뒷받침하는 것만):
- **Why**(필요/동기) / **When**(계기/시간) / **Where**(장소/맥락) / **While**(병행 활동) / **With Whom**(사회적 맥락) / **With What**(보완 제품) / **hoW Feeling**(감정 상태)
- JSON keys: why / when / where / while_ / with_whom / with_what / how_feeling

{{evidence_section}}

# Language
**{{response_language}}로 유효한 JSON 형식으로 응답해 주세요.**
`rtb`는 원본 인용의 언어를 verbatim으로 보존(구체 수치·브랜드·기능 포함).

**[규칙 B] 인용부호 제거:** URL·출처 콘텐츠 인용 시 `""`·`''`를 결과에서 제거(내용만 verbatim, 부호 없이). (JSON 문자열 구분자용 큰따옴표는 유지.)

# Quantity Constraint (CRITICAL)
정확히 {{requested_count}}개의 CEP 객체를 반환. 최상위 JSON 배열 길이 = {{requested_count}}.
- **[M8 우선순위]** Why+When은 하드(못 채우면 그 카드 만들지 않음). +2축은 개수 확보를 위해 완화 가능(단 축 창작 금지). 그래도 근거가 부족하면 **근거>개수** — 디테일을 지어내 개수를 맞추지 말고 덜 사용된 섹션에서 더 빈약한(그러나 근거 있는) 카드를 만드세요. 근거로 도저히 채울 수 없으면 수량 미달을 감수합니다.

---

# OUTPUT RULES (STRICT, JSON-ONLY)
- 오직 하나의 유효한 JSON 값만 반환.
- 코드 펜스(```) 금지. JSON 외 산문·설명·목록 금지. 후행 쉼표 금지. 모든 키·문자열 값에 큰따옴표.
- **`nanoIntents` 미포함(규칙 C).** `situation`은 '~하는 순간/때'로 끝나고 ≈40자(규칙 A·D), M2·M3·M5·M6 만족. `context`·`w7`·`evaluation`·`cep_score` 포함.
- 최상위 JSON은 길이가 정확히 {{requested_count}}인 배열.
````

---

### 로케일 안내

- 본 초안은 **KR** 기준. JP/EN은 동일 취지(M6~M11 + 압축/역할분리)를 수동 반영해야 함. (종결어미·범주화·기능배제·페르소나 축 표현은 언어별 자연스러운 등가로 조정.)

### 검증 제안 (승인 후)

- 재생성 후 방법론 지표 측정: ① 기능·편익 혼입 situation 비율 ↓ ② 사용후기형 situation 비율 ↓ ③ w7 6W1H(Why+When+2) 충족률 ↑ ④ situation 평균 길이 ≈40자 수렴 ⑤ 페르소나 축 분산도 ↑.
- grounding 회귀 불변: v1.4.0 대비 partial/vague/속성근거없음 플래그가 악화되지 않아야 함(M9 압축이 근거를 과도하게 깎지 않는지, M6 이동이 kbf_hints 그라운딩을 깨지 않는지).
- **파서 비파괴 확인(필수)**: 재생성 harvest에서 `cep`(=situation) 필드가 이전처럼 채워지는지(빈값·전카드 fail 재발 없는지) 먼저 확인. context/evaluation은 파싱 추가 여부와 별개로 기존 cep 노출이 정상이어야 함.
