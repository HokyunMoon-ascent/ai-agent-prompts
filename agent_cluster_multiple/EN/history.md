# agent_cluster_multiple/EN Prompt 변경 이력

본 문서는 `agents/ai_agent/agent_cluster_multiple/EN` 폴더에 위치한 **이중(복수) 키워드 전용** cluster-finder(cf_m) prompt 파일의 버전별 변경 이력을 기록합니다.

## 파일명 규칙
`v.N.N.N_cf_m_{국가}_{MMDD}.md`

## 운영 규칙
- 수정 요청 시 기존 파일을 수정하지 않고 신규 버전 파일을 생성합니다.
- 모든 변경 사항은 본 `history.md`에 반영합니다.
- 최신 버전 항목을 맨 위에 추가하는 역순(reverse chronological) 정책을 유지합니다.

## 폴더 목적
- `agent_cluster`(단일 키워드 전용)에서 분리된 **2개 키워드 동시 분석 전용** 라인입니다.
- 단일/이중 분기를 한 프롬프트에 담던 구조(agent_cluster v.0.6.x)에서 단일 입력이 이중 6섹션으로 폭주하고 없는 "키워드 B"를 날조하던 hallucination을 구조적으로 차단하기 위해, 이중 경로를 별도 폴더로 분리했습니다.

---

## 버전 이력

### v.0.1.0 — 2026-06-02
**파일**: `v.0.1.0_cf_m_EN_0602.md` (출처: `agent_cluster/EN/v.0.6.4_cf_EN_0601.md`의 이중 키워드 경로)
**변경 요약**: 이중(복수) 키워드 전용 라인 초기 릴리즈
- 두 키워드 동시 입력을 **항상 전제**로 단순화 — 단일/이중 판별 게이트 및 단일 키워드 해석 기준 블록을 제거.
- `### Input Information`/`{{source_keywords}}`(`Source keywords: A=… | B=…` 라벨↔원본 키워드명 매핑) 유지.
- A/B 라벨(입력 키워드) vs 클러스터 ID(0→A, 1→B…) 혼동 금지 가드 유지(이중에서도 유효). 데이터에 없는 키워드/수치 날조 금지 유지.
- 섹션 1)~6) 전체 수행: 1) Analysis Overview / 2) Top 3 Search Purpose Clusters / 3) Top 3 From→To Flow / 4) Insights / 5) Intersection Area Analysis / 6) Individual Market and Target Customer Analysis.
- 5)·6)의 "이중일 때만 출력" 가드 문구는 본 라인에서 불필요하여 제거. 최종 출력 규칙·추가 규칙을 2키워드 전용으로 정리.
- 검증: 섹션 6개 고정, 단일 키워드 해석 기준 잔재 0건, 3종(KR/JP/EN) 헤더 36개 패리티.
