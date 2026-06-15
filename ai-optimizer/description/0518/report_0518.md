<!-- report_0518.md -->

# ai-optimizer 종합 보고서

> **작성일**: 2026-05-18 / **대상 버전**: v.0.1.0 ~ v.0.2.6 / **언어**: KR (정본) · JP/EN (프롬프트 동기화)
> **선행 문서**: `description/history.md` (v.0.2.2 → v.0.2.6 누적 diff)
> **본 보고서의 위치**: 외부 협업자(엔지니어·QA·신규 멤버)가 한 파일로 ai-optimizer의 역할·이력·산출물·산정 규칙을 파악하기 위한 정리본. history.md는 그대로 유지하며 본 보고서에서는 인용·재구성만 합니다.

---

## 1. 개요

### 1.1 ai-optimizer가 무엇인가

ascent의 **AI 응답 최적화(AI Overview Optimization, AIO) 에이전트 묶음**으로, 사용자 의도(CEP) 기반 AI 응답에 자사 콘텐츠가 더 잘 반영되도록 (a) 자사 페이지의 **의미적 갭**을 진단하고, (b) 그 갭을 메우는 **온드 · 언드 · 응답 진단** 액션 플랜을 산출하는 4 에이전트의 프롬프트 라이브러리입니다. 컨설팅 의뢰자(마케터·브랜드 담당자)가 결과를 바로 읽고 의사결정할 수 있도록, 분야 전문 용어를 추방하고 **표 + 통합 해설** 단위로 출력하는 것을 원칙으로 합니다.

### 1.2 에이전트 4종 역할 요약

| 에이전트 | 한 줄 역할 | 입력 자산 (자사 URL 유무) |
|---|---|---|
| **gap** | 자사 페이지(A) ↔ AI 응답(C) 비교로 **자사 페이지에 빠진 의미적 영역**을 도출 | URL 필수 |
| **owned** | gap 결과를 받아 **자사 페이지에서 보강·신설**할 IA(H1/H2)를 제안 | URL 필수 |
| **earned** | gap 결과 중 자사 페이지로 회수 불가능한 영역을 **외부 매체·커뮤니티·리뷰어 유도** 액션으로 분해 | URL 필수 |
| **noneURL** | 자사 URL 없이 **AI 응답 자체의 구조·인용 출처**를 진단 (URL 사전 단계용) | URL 없음 |

### 1.3 입출력 데이터 흐름 — ABC 프레임

```
A. 자사 페이지 파싱 콘텐츠 (page_content_A)   ─┐
B. CEP 기반 AI 검색 질문    (user_prompt_B)    ├─→  gap / owned / earned
C. AI 응답 N개              (ai_responses_C)   ─┘    (URL 모드)
                                                ─→  noneURL (B + C만 사용)
```

- **A**: 사용자 입력 URL 크롤링 → 본문 텍스트만 추출 (메뉴·CTA·푸터 제거)
- **B**: NotebookLM 워크플로우에서 CEP별로 작성한 프롬프트
- **C**: 동일 B를 GPT 3회 + Google AI Overview 3회 반복 호출 (총 6건)
- **세션 컨텍스트**: `{{prev_q}}`, `{{prev_a}}`, `{{user_question}}` 3개 추가 슬롯 (대화 맥락용)
- **명세 원본**: `agent_aiOpt_{gap,owned,earned,noneURL}/dev_request_aiOpt_*_*.md`

### 1.4 폴더 구조

```
ai-optimizer/
├─ agent_aiOpt_gap/         ┐
├─ agent_aiOpt_owned/       │  4 에이전트 — 각 폴더에 KR / JP / EN 서브 + dev_request_*.md
├─ agent_aiOpt_earned/      │
├─ agent_aiOpt_noneURL/     ┘
├─ aiOpt-src/               ─ 공용 입력 샘플 (A / B / C 1·2·3 × GPT·Google)
└─ description/             ─ 요청서·표 가이드·히스토리
   ├─ 0514/description.md      (gap 최초 요청 — 2026-04-30)
   ├─ 0515/                    (v.0.2.3 트리거)
   │  ├─ gap_description.md
   │  ├─ earned_description.md
   │  ├─ owned_description.md
   │  └─ table_layout.md
   ├─ 0518/description.md      (v.0.2.6 트리거)
   ├─ history.md               (v.0.2.2 → v.0.2.6 diff 누적, 197 lines)
   └─ report_0518.md           ← 본 문서
```

---

## 2. 작업 내역 (버전별 타임라인)

> v.0.2.2 이후는 `history.md`에 상세 diff가 있으니 본 장은 **요지·연결 흐름·보류 항목** 중심으로 요약하고, 자세한 변경 라인은 `[참조: history.md §v.0.2.x]`로 위임합니다.

### 2.1 v.0.1.0 — 4 에이전트 골격 (2026-04-30 / 2026-05-04)

- **gap KR**: 2026-04-30 `v.0.1.0_aiOpt_gap_KR_0430.md` 최초 작성 (description/0514 요청 기반)
- **owned · earned · noneURL KR/JP/EN**: 2026-05-04 동시 생성
- 모든 에이전트는 페르소나 1문장 + ABC 입력 슬롯 + 4섹션 출력(분석 개요 / 진단 / 제안 / 인사이트) 골격으로 출발
- **gap의 ABC 프레임 정의** (description/0514/description.md):
  - A = 자사 페이지 파싱 콘텐츠
  - B = CEP별 프롬프트
  - C = B로 받은 AI 응답
  - C 기준으로 (a) 자사 브랜드 노출 여부, (b) B에 응당 다뤄야 하지만 A에는 없는 영역 두 가지를 분리 진단

### 2.2 v.0.2.0 — 3로케일 본격 분기 (2026-05-04)

- KR 정본 외 JP/EN 동시 분기 시작
- 답변 파일(answer.md)이 KR 기준 정본으로 등장 (`.gdoc` 4건은 v.0.2.0 시점의 Google Docs 백업)
- 출력 포맷에 `:::accordion` 블록 + `**➊➋➌**` + `:k[..]` 키워드 표기 도입

### 2.3 v.0.2.1 — 불릿 기반 출력 안정화 (2026-05-06)

- 불릿 리스트 기반 출력으로 4섹션 구조 안정화
- 모바일 카드 렌더링에서 가독성·우선순위 식별 부족이라는 한계 노출 (다음 라운드 v.0.2.3에서 표 도입의 직접 동기)

### 2.4 v.0.2.2 — 4섹션 구조 완성 (2026-05-14)

- `1) 분석 개요 / 2) 갭 진단 내용 요약 / 3) 개선 제안 / 4) 인사이트` 4섹션이 4 에이전트 공통 표준이 됨
- 마케터 사용 흐름에서 분석 개요·인사이트의 중복·시선 분산 문제가 누적됨 → 다음 라운드 압축의 직접 트리거

### 2.5 v.0.2.3 — 4섹션 → 1~2섹션 대대적 압축 (2026-05-15 17시대)

[참조: history.md §v.0.2.2 → v.0.2.3]

- **gap**: 3섹션 → 2섹션 (`1) 브랜드 노출 분석` / `2) 의미적 갭 매핑`)
- **owned**: 4섹션 → 1섹션 (`# 개선 제안`)
- **earned**: 4섹션 → 매트릭스 1~2개 단일 흐름
- **noneURL**: 4섹션 → 2섹션 (`1) 응답 구조 분석` / `2) 브랜드 언급 맥락 및 답변 인용 출처 분석`)
- 공통 분석 원칙 7·8·9번 신설 (토픽 그룹 구성 · 표 셀 표기 · 표 직후 해설)
- 마케터 친화 톤 도입 ("~입니다" 정중체, 분야 전문 용어 미노출)
- **산출물**: KR 16개 (4 prompt + 4 answer × KR — JP/EN 미동기화)
- **비고**: history.md 본문은 사후 재구성(v.0.2.3 항목이 history 작성 시점에 누락)

### 2.6 v.0.2.4 — 통합 해설 400자 압축 (2026-05-15 18시대)

[참조: history.md §v.0.2.3 → v.0.2.4]

- 압축 단위: 큰 단락당 통합 해설 1개 = **400자 내외**
- sub-table 사이 중간 해설 제거, 큰 단락 끝에 통합 해설만 배치
- gap 특화: ➍ 부족 신호 가설 → 통합 해설 마지막 메인 불릿로 흡수 (별도 ➍ 블록 제거)
- **산출물**: 16개 (KR prompt 4 + KR answer 4 + JP/EN prompt 8)

### 2.7 v.0.2.5 base → in-place 누적 패치 — 3컬럼 표 표준 완성 (2026-05-15 base → 2026-05-18 in-place)

[참조: history.md §v.0.2.4 → v.0.2.5 / v.0.2.5 in-place]

- **base (v.0.2.5)**: 다중 표 큰 단락에서 ➊ 토픽 그룹 정의 표 1개만 유지, 나머지 표 정보는 **500자 통합 해설**에 흡수
- **in-place 누적 패치 (1차 ~ 7차)**: UI 적용 후 표 셀이 좁아 가독성 저하 → 표를 줄이고 컬럼을 줄이는 누적 패치
  - 모든 표를 **3컬럼**으로 통일 (4·5컬럼에서 단계적 축소)
  - 알파벳 라벨 `**A. 토픽명**` 도입 (좁은 셀에서 그룹 식별을 보장)
  - gap §1 ↔ §2 cross-section 라벨 정합 (좌·우 페어 = §1·§2 모두 A)
  - **earned**: 인용 현황 + 액션 두 표 → 단일 토픽별 액션 표 + 500자 해설로 병합
- **JP/EN 동기화 보류** (in-place 패치는 KR 한정)
- **다음 라운드 보류**: 표현 다듬기 (허브·신뢰 통제권 등) · 라벨 어휘 (위험/양호/안전) · gap §2 갭 상세 통합 → 모두 v.0.2.6에서 일괄 처리

### 2.8 v.0.2.6 — 0518 description 적용 (2026-05-18)

[참조: history.md §v.0.2.5 → v.0.2.6 / description/0518/description.md]

1. **표현 다듬기**: 허브 → 안내 페이지, 흡수 방식 → 개선 방법, 신뢰 통제권 → 외부 페이지를 인용의 증거로 사용, 통증 RTB → 커뮤니티 후기를 인용, 비대칭 구도 → 특정 제품에 편중 등
2. **라벨 어휘 통일**: 🔴 결정적 / 🟡 기회·🟡 보강 (2단계) → **🔴 위험 / 🟡 양호 / 🔵 안전** (3단계)
3. **표 헤더 통일**: gap §2 · earned · owned `토픽 그룹 / 개선 우선 순위 / 해결 전략` 3컬럼 정렬
4. **gap §2 ➋ 갭 상세 블록 제거**: 7항목 별도 블록 폐지 → 위험 그룹은 메인 불릿 + 근거·필요 단서 하위 불릿로 흡수
5. **noneURL §1 결론 컬럼 삭제**: 3컬럼 → 2컬럼 (`응답 / 본론`)
6. **noneURL ➍ → ➋ 재번호 정정**: §2 sub-block 2개 정합
7. **earned 셀 액션 재구성**: `AI가 인용할 핵심 표현` 셀 → `해결 전략` 셀 (채널·형식 + AI 인용 후보 + 동사 한 줄)
8. **토픽 그룹 카디널리티 3개 고정**: 4 에이전트 × 3 로케일 모두 정확히 3개 (A · B · C)
9. **"그룹" → "토픽 그룹" 어휘 통일**: 본문·표 헤더·셀·해설·체크리스트 일괄 치환 (동사형 "그룹화" 보존)
- **산출물**: 16 파일 (KR prompt 4 + JP/EN prompt 8 + KR answer 4)

---

## 3. 주요 산출물 카탈로그

### 3.1 산출물 통계 — 파일 카운트 (2026-05-18 기준)

| 폴더 | KR `.md` | JP `.md` | EN `.md` | 합계 |
|---|---:|---:|---:|---:|
| agent_aiOpt_gap | 15 | 10 | 10 | 35 |
| agent_aiOpt_owned | 15 | 10 | 10 | 35 |
| agent_aiOpt_earned | 15 | 10 | 10 | 35 |
| agent_aiOpt_noneURL | 16 | 10 | 10 | 36 |
| **소계 (4 agents)** | **61** | **40** | **40** | **141** |

> 카운트 제외: 각 KR 폴더의 `v.0.2.0_aio_*_answer.md.gdoc` 4건 (Google Docs 백업). noneURL/KR이 +1인 이유는 v.0.2.2 시점에 `_0514.md`와 `_0515.md` 2개가 공존하기 때문.

### 3.2 프롬프트 파일 — 파일명 규칙 및 현행 권장

```
파일명: v.{버전}_aiOpt_{type}_{LOCALE}_{MMDD}.md
   예 : v.0.2.6_aiOpt_gap_KR_0518.md

현행 권장 (v.0.2.6) — 12 파일
  agent_aiOpt_gap/{KR,JP,EN}/v.0.2.6_aiOpt_gap_{KR,JP,EN}_0518.md
  agent_aiOpt_owned/{KR,JP,EN}/v.0.2.6_aiOpt_owned_{KR,JP,EN}_0518.md
  agent_aiOpt_earned/{KR,JP,EN}/v.0.2.6_aiOpt_earned_{KR,JP,EN}_0518.md
  agent_aiOpt_noneURL/{KR,JP,EN}/v.0.2.6_aiOpt_noneURL_{KR,JP,EN}_0518.md
```

### 3.3 답변 샘플 파일 (KR 정본)

```
파일명: v.{버전}_aiOpt_{type}_answer.md
   예 : v.0.2.6_aiOpt_gap_answer.md

현행 권장 (v.0.2.6) — 4 파일
  agent_aiOpt_gap/KR/v.0.2.6_aiOpt_gap_answer.md      (6 토픽 라벨 행 = §1 3 + §2 3)
  agent_aiOpt_owned/KR/v.0.2.6_aiOpt_owned_answer.md  (3 토픽 라벨 행)
  agent_aiOpt_earned/KR/v.0.2.6_aiOpt_earned_answer.md (3 토픽 라벨 행)
  agent_aiOpt_noneURL/KR/v.0.2.6_aiOpt_noneURL_answer.md (3 토픽 라벨 행)
```

> JP/EN 답변은 정본이 없으며 `.gdoc` 잔재만 존재. 답변은 KR만 정본.

### 3.4 사용 현재 권장 세트 — 총 16개 파일 (v.0.2.6)

```
프롬프트 12 = 4 에이전트 × 3 로케일
답변      4 = 4 에이전트 × KR
─────────────────────
합계 16
```

### 3.5 개발 요청서 (4건)

| 에이전트 | 파일 | 핵심 변수 슬롯 |
|---|---|---|
| gap | `dev_request_aiOpt_gap_0430.md` | `{{page_content_A}}` · `{{user_prompt_B}}` · `{{ai_responses_C}}` · `{{prev_q}}` · `{{prev_a}}` · `{{user_question}}` (총 6) |
| owned | `dev_request_aiOpt_owned_0504.md` | 동일 6 슬롯 (+ 선택 `{{gap_analysis_output}}`) |
| earned | `dev_request_aiOpt_earned_0504.md` | 동일 6 슬롯 (+ 선택 `{{gap_analysis_output}}`) |
| noneURL | `dev_request_aiOpt_noneURL_0504.md` | `{{user_prompt_B}}` + `{{ai_responses_C}}` + 세션 컨텍스트 3종 |

### 3.6 description 자료

| 파일 | 역할 |
|---|---|
| `0514/description.md` | gap 에이전트 최초 요청서 (ABC 프레임 정의, 임베딩 비교 검토안 포함) |
| `0515/table_layout.md` | v.0.2.3 표 레이아웃 가이드 (`:::accordion` + ➊➋➌ + 매트릭스 + 글머리 해설 600자) |
| `0515/gap_description.md` | v.0.2.3 gap 트리거 요청서 |
| `0515/earned_description.md` | v.0.2.3 earned 트리거 요청서 |
| `0515/owned_description.md` | v.0.2.3 owned 트리거 요청서 |
| `0518/description.md` | v.0.2.6 트리거 — 어휘 다듬기·라벨·표 헤더 재설계 |
| `history.md` | v.0.2.2 → v.0.2.6 누적 diff (197 lines) |
| `report_0518.md` | (본 문서) |

### 3.7 공용 src 자료 (`aiOpt-src/`)

| 파일 | 역할 |
|---|---|
| `A_가이드에 사용하는 내 페이지를 파싱한 콘텐츠 .md` | 자사 페이지 파싱 샘플 |
| `B_AI에 던지는 질문 (CEP별로 만든 프롬프트).md` | CEP 기반 AI 검색 프롬프트 샘플 |
| `C_B프롬프트로 받은 AI 응답_{1,2,3} (GPT).md` | GPT 응답 3건 |
| `C_B프롬프트로 받은 AI 응답_{1,2,3} (Google).md` | Google AI Overview 응답 3건 |

> 총 9 파일. dev_request 명세상 GPT 3건 + Google 3건 = 6건 AI 응답이 분석 단위.

---

## 4. 산정 규칙 (Production Rules)

> **본 장은 v.0.2.6 기준 현행 규칙**입니다. 산출물 정합성 보장의 한 곳 정리본 — 여기 적힌 규칙이 위반되면 prompt-verifier가 에러를 띄웁니다.

### 4.1 출력 섹션 구조 (에이전트별)

| 에이전트 | 노출 섹션 | 비고 |
|---|---|---|
| **gap** | 2섹션: `1) 브랜드 노출 분석` · `2) 의미적 갭 매핑` | v.0.2.3 압축 결과 |
| **owned** | 1섹션: `# 개선 제안` | 내부 분석 개요·갭 진단·인사이트는 사고 단계로만 |
| **earned** | 단일 흐름: 토픽별 액션 표 1개 + 통합 해설 | v.0.2.5 in-place 6차에서 2표 → 1표 병합 |
| **noneURL** | 2섹션: `1) 응답 구조 분석` · `2) 브랜드 언급 맥락 및 답변 인용 출처 분석` | §2는 ➊ 토픽 그룹 정의 + ➋ 자사 진입 가설 |

### 4.2 표 표준 — 3컬럼 룰 (6개 표 전체)

| 파일 | 표 | 컬럼 구성 | 행 수 |
|---|---|---|---|
| gap §1 | ➊ 토픽 그룹 정의 | 토픽 그룹 / 묶인 세부 토픽 / 자사 대응 액션 | 3 (위험 격차 순) |
| gap §2 | ➊ 콘텐츠 갭 표 | 토픽 그룹 / 개선 우선 순위 / 해결 전략 | 3 (라벨 순) |
| earned | 토픽별 액션 (단일) | 토픽 그룹 / 개선 우선 순위 / 해결 전략 | 3 (라벨 순) |
| owned | ➊ 토픽 그룹 정의 | 토픽 그룹 / 개선 우선 순위 / 해결 전략 | 3 (라벨 순) |
| noneURL §1 | ➊ 응답 흐름 요약 | 응답 / 본론 (추천 순서·비중) | 응답 수만큼 (예외: 2컬럼) |
| noneURL §2 | ➊ 토픽 그룹 정의 | 토픽 그룹 / 묶인 세부 토픽 / AI 응답에서 다뤄진 방식 | 3 |

### 4.3 토픽 그룹 카디널리티 — 정확히 3개 (A · B · C)

- 4 에이전트 × 3 로케일 모두 **정확히 3개 토픽 그룹** (`A.` / `B.` / `C.`)
- 신뢰 보강용 별도 그룹(구 E.) 폐지 → 기존 3개 중 가장 적합한 곳의 통합 해설 메인 불릿로 흡수
- **cross-section 정합**: gap §1 ↔ §2 동일 라벨 (좌·우 페어 = §1·§2 모두 A)
- 라벨 표기: `**A. 토픽명**` (좁은 셀에서 기호로 그룹 식별)
- 잔재 grep: `4~5|3~5|4–5|3–5|4〜5|3〜5` 0건

### 4.4 우선순위 라벨 — 3단계

| 신규 (v.0.2.6) | 폐기 (v.0.2.5 이전) |
|---|---|
| 🔴 위험 | 🔴 결정적 |
| 🟡 양호 | 🟡 기회 |
| 🔵 안전 | 🟡 보강 |

### 4.5 통합 해설 분량 (글자수)

| 단락 유형 | 분량 |
|---|---|
| 단일 표 단락 (earned · gap §2 · noneURL §1) | **400±50자** |
| 다중 표 압축 단락 (gap §1 · owned · noneURL §2) | **500±50자** |
| noneURL §2 ➋ 자사 진입 가설 | **300자** (별도 유지) |

- 메인 불릿 = 핵심 메시지(굵게), 하위 불릿 = 근거·세부 사항으로 계층화
- 표 셀의 숫자·기호를 그대로 읽지 말고 한 단계 위로 해석
- 점유 제품·매체·H1 후보·도메인 등 결정적 인용은 **굵게** 보존

### 4.6 어휘 규칙 — 마케터 친화

#### 추방 (출력 본문 등장 금지)

```
허브 · 흡수 방식 · 신뢰 통제권 · 통증 RTB · 비대칭 구도
대표 해답 자리 · 조건부 대안 자리 · 1순위를 정당화
RTB · KBF · PDP · FAQ · 매트릭스 · 사분면 · 4축 · 5분류 · 프레임
효익 진술 · 결정 직전 인용원 · 구조적 약점 · 균형 인용 신호 · 무게중심
정합성 · 흡수 가능성 · consensus · variance · 진영 (단독)
```

#### 권장 치환

| 폐기 | 권장 |
|---|---|
| 허브 | 안내 페이지 |
| 흡수 방식·흡수 가능성·흡수 | 개선 방법·개선 가능성·녹여 넣음 |
| 신뢰 통제권 | 외부 페이지를 인용의 증거로 사용 |
| 대표 해답 자리 | 주요 해결책으로 안내 |
| 조건부 대안 자리 | 그 외 제품으로는 X 등장 |
| 1순위를 정당화 | 주요 근거로 안내 |
| 통증 RTB를 떠받치다 | 커뮤니티 후기를 인용 |
| 비대칭 구도 | 특정 제품에 편중 |
| 자사 페이지가 비어 있는 토픽 | 자사 페이지에서 부족한 토픽 |
| 무게중심 | 주된 내용 |

#### "그룹" → "토픽 그룹" 통일

- 본문·표 헤더·표 셀(`(그룹명)` → `(토픽 그룹명)`)·해설·체크리스트 일괄 치환
- **보존 허용**: 동사형 "그룹화 / グループ化 / grouping", 라벨+그룹 결합어 ("🔴 위험 그룹 / 🟡 良好グループ / 🔴 Risk groups")

### 4.7 셀 표기 · 출력 톤

- 셀 기호: `✅ N/M` (강점 점유) · `⚠️ N/M` (부분 점유) · `❌ 0/M` (미점유) — M = 분석 대상 AI 응답 총 건수
- 정중체: 어미 **"~입니다"** 일관 (KR), 정중 마감
- **One-Line Rule**: 표 셀·해설 하위 불릿 한 줄 원칙
- `:::accordion` 블록 **미사용** (v.0.2.3 이후 폐지)
- 토픽 그룹 좌측 행 고정 (모든 매트릭스)
- 인용 형식: `:k[엔티티]` (해설 단계에서만 사용, 표 셀에서는 미사용)

### 4.8 SAMPLE_DATA 불변 원칙

```
<!-- SAMPLE_DATA:BEGIN type=X -->
… (런타임 결합 블록)
<!-- SAMPLE_DATA:END -->
```

- 위 블록은 **3로케일 byte-equal**
- 번역·편집 절대 금지
- `_SAMPLE_DATA_SOURCE.md`가 정본
- 위반 시 prompt-verifier가 ERROR

### 4.9 3로케일 동기화 규칙

- KR/JP/EN line·marker 카운트 **완전 일치**
- `:k[..]` 태그 보존 (번역 시에도 동일 키 유지)
- 작업 흐름: KR 패치 → `/prompt-sync <KR_path>` → JP/EN 자동 동기화 → `/prompt-verify` 통과
- 단일 로케일 패치(KR-only)는 명시적으로 history.md에 기록 (예: v.0.2.5 in-place)

### 4.10 답변 파일 규칙

- KR만 정본 (JP/EN 답변 없음)
- 표 행 수: gap = **6** (§1 3 + §2 3), earned · owned · noneURL 각 **3**
- 라벨 prefix: `**A.**` / `**B.**` / `**C.**` (gap만 §1·§2 동일 라벨 정합)
- 우선순위 행 정렬: 위험 → 양호 → 안전

---

## 5. 검증 도구 (Verification Recipes)

### 5.1 핵심 grep 명령

```bash
# 글자수 (통합 해설)
awk '/통합 해설/{f=1} f && /^$/{f=0; exit} f' <file> | tr -d '[:space:]' | wc -m

# SAMPLE_DATA byte-equal (KR/JP/EN 비교)
for L in KR JP EN; do
  awk '/SAMPLE_DATA:BEGIN/,/SAMPLE_DATA:END/' \
    agent_aiOpt_gap/$L/v.0.2.6_aiOpt_gap_${L}_0518.md | md5
done   # 3개 해시 모두 동일해야 함

# 표 컬럼 수
grep -E "^\| ----" <file> | awk -F'|' '{print NF-2, "columns"}'

# 라벨 일관성 (A/B/C 행 카운트)
grep -cE "^\| \*\*[A-C]\." <answer.md>
#   gap_answer = 6, owned/earned/noneURL_answer = 3

# 어휘 잔재 점검 (0건 이어야 함)
grep -n "허브\|흡수 방식\|신뢰 통제권\|통증 RTB\|비대칭 구도\|대표 해답 자리\|조건부 대안 자리" <file>

# 카디널리티 잔재 (0건 이어야 함)
grep -nE "4~5|3~5|4–5|3–5|4〜5|3〜5" <file>

# 신규 라벨 sanity
grep -n "🔴 위험\|🟡 양호\|🔵 안전\|개선 우선 순위\|해결 전략\|개선 방법\|안내 페이지" <file>
```

### 5.2 슬래시 커맨드 도구 (CLAUDE.md 기재)

| 커맨드 | 용도 |
|---|---|
| `/prompt-verify [path]` | SAMPLE_DATA · keyword_format_rules · 3로케일 parity 검증 (read-only) |
| `/prompt-sync <source>` | 1로케일 → 다른 2로케일 자동 propagation (translator 호출) |
| `/prompt-new <type>` | KR/JP/EN 스캐폴드 동시 생성 (SAMPLE_DATA 자동 주입) |
| `/prompt-migrate [--apply]` | 레거시 폴더 → `0429/agents/` 일괄 이동 (기본 dry-run) |

### 5.3 서브에이전트

| 에이전트 | 역할 |
|---|---|
| `prompt-verifier` | MARS-style critic. severity-classified findings 반환 (read-only) |
| `prompt-translator` | KR↔JP↔EN 번역. SAMPLE_DATA byte-equal, `:k[..]` 태그 untouched 보장 |

---

## 6. 미해결 · 후속 작업 (Open Items)

| # | 항목 | 우선순위 | 메모 |
|---|---|---|---|
| 1 | JP/EN 답변 파일 부재 | 보통 | KR만 정본. JP/EN 답변 신규 작성 시점 미정 |
| 2 | v.0.2.5 in-place 패치의 JP/EN 동기화 | 부분 해소 | v.0.2.6에서 표 표준·라벨·어휘는 일괄 흡수됨. 잔여 미동기 항목은 v.0.2.6 grep으로 0건 확인 가능 |
| 3 | 임베딩 기반 비교 전환 (description/0514) | 보통 | 단어·문장 일치 한계 → 의미 임베딩 검토. 엔지니어 협의 단계. 검증 사이클 우선 |
| 4 | 0518 description 미반영 잔재 | 낮음 | gap §2 갭 상세 흡수 형태, owned 어휘 일부 다듬기 |
| 5 | description/0518 추가 피드백 — 차기 라운드 | 낮음 | gap §2 컬럼 재구성 (`그룹 / 콘텐츠 액션 / 우선순위` → `그룹 / 개선 우선순위 / 해결 전략`)은 v.0.2.6에서 처리 완료 / 콘텐츠 갭 + 갭 상세 통합 검토는 진행 중 |

---

## 7. 참고 문서

| 종류 | 경로 |
|---|---|
| 누적 diff | `description/history.md` |
| 최초 요청 (gap) | `description/0514/description.md` |
| v.0.2.3 트리거 | `description/0515/{gap,earned,owned}_description.md`, `description/0515/table_layout.md` |
| v.0.2.6 트리거 | `description/0518/description.md` |
| 개발 명세 | `agent_aiOpt_{type}/dev_request_aiOpt_{type}_*.md` |
| 프로젝트 가이드 | `../../../CLAUDE.md` (프로젝트 루트) |
| 에이전트 매니페스트 | `../../../0429/agents/_MANIFEST.md` |
| SAMPLE_DATA 정본 | `../../../0429/agents/_SAMPLE_DATA_SOURCE.md` |

---

## 부록 A — 산출물 통계 (버전별 누적)

| 버전 | 산출물 (해당 라운드 신규) | locale | 합계 |
|---|---|---|---|
| v.0.1.0 | gap KR 1 + owned/earned/noneURL × 3 locale × 1 = 9 → **10** | KR/JP/EN | 10 |
| v.0.2.0 | KR prompt 4 + KR answer 4 + JP/EN prompt 8 + .gdoc 4 | KR/JP/EN | 20 |
| v.0.2.1 | KR/JP/EN prompt 12 | KR/JP/EN | 12 |
| v.0.2.2 | KR prompt 4 (gap·owned·earned·noneURL_0514) + noneURL_0515 + KR answer 4 + JP/EN prompt 8 | KR/JP/EN | 17 |
| v.0.2.3 | KR prompt 4 + KR answer 4 | KR만 | 8 |
| v.0.2.4 | KR prompt 4 + KR answer 4 + JP/EN prompt 8 | KR/JP/EN | 16 |
| v.0.2.5 (base) | KR prompt 4 + KR answer 4 + JP/EN prompt 8 | KR/JP/EN | 16 |
| v.0.2.5 (in-place) | KR prompt 3 + KR answer 3 patch | KR만 | 누적 6 갱신 |
| v.0.2.6 | KR prompt 4 + JP/EN prompt 8 + KR answer 4 | KR/JP/EN | 16 |
| **총계** | **141 markdown** (+ 4 .gdoc) | — | — |

---

## 부록 B — v.0.2.6 KR 답변 파일 토픽 그룹 라벨 매핑

| 에이전트 | 라벨 매핑 |
|---|---|
| **gap** | A. 좌·우 페어 🔴 / B. 신뢰 보강 🔴 (구 E.) / C. 엄지 측면 버튼 🟡 (구 B.) — §1·§2 동일 라벨 |
| **owned** | A. 좌·우 페어 🟡 / B. 외부 후기·매체 인용 안내 페이지 🔴 / C. 적응 단계 가이드 🟡 |
| **earned** | A · B · C 3개 (구 D. 실증 후기 🔵 안전 드롭, 종합 라인에 자연어 흡수) |
| **noneURL** | A · B · C 3개 (구 C. 엄지 측면 버튼 드롭, 구 D. 자세·환경 → C.로 재라벨링) |

---

## 부록 C — One-Pager 요약 (스캔용)

- **에이전트 4종**: gap (자사 갭 진단) · owned (자사 IA 제안) · earned (외부 매체 유도) · noneURL (URL 없는 응답 진단)
- **현재 권장 버전**: v.0.2.6 (2026-05-18)
- **출력 단위**: 3컬럼 표 1개 + 통합 해설 400/500자
- **토픽 그룹**: 정확히 3개 (A · B · C)
- **우선순위 라벨**: 🔴 위험 / 🟡 양호 / 🔵 안전
- **표 헤더 표준**: `토픽 그룹 / 개선 우선 순위 / 해결 전략` (gap §2 · earned · owned 공통)
- **금지 어휘**: 허브 · 흡수 방식 · 신뢰 통제권 · RTB · KBF · PDP · FAQ
- **3로케일 동기화**: SAMPLE_DATA byte-equal + line·marker 카운트 일치
- **검증**: `/prompt-verify` 한 번이면 충분

— 끝 —
