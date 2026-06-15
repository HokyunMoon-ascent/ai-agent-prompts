# inService ↔ repo 프롬프트 비교 리포트

생성일: 2026-06-15 · 비교 방식: **콘텐츠 레벨**(런타임 스캐폴딩·버전 주석 등 포맷 노이즈 정규화 후 지시 내용 비교)

- **서비스(inService)** = `admins.listeningmind.com/hubble/gpt-prompt`에서 스크랩한 현재 적용본
- **repo** = 저장소의 캐논 최신 버전

---

## ✅ 해결 현황 (2026-06-15 서비스 배포 반영)

관리자 패널 재배포 확인됨 — 아래 6개 항목 모두 당일 갱신 완료.

| agent | 배포 시각 | 조치 내용 | 상태 |
|---|---|---|---|
| agent_past_current_cluster | 17:39:40 | repo v.0.1.8 배포 (+ EN Path Finder 오등록 정정) | ✅ 해결 |
| agent_past_current_path | 17:40:41 | repo v.0.1.8 배포 | ✅ 해결 |
| agent_cluster_selected | 17:48:23 | v.0.1.7 스타일(헤더 ⒈/불릿 ・/title すべてを見る) | ✅ 해결 |
| agent_persona_specialist | 17:54:07 | `cluster_csv` 트레일러 정렬 (EN/KR 트레일러 추가, JP 키 정정) | ✅ 해결 |
| agent_query | 18:01:34 | v.0.4.6 (`query_csv` 트레일러 + §4 인라인) | ✅ 해결 |
| GEO_prompt_builder | 18:08:08 | `context_csv` 키 정정 | ✅ 해결 |

**`agent_system_prompt` (🔀 혼재) → 통합본 v.0.1.4 작성 완료 / ⏸ 서비스 배포 보류**
- ⏸ **배포 안 함**: 시스템 프롬프트 반영 영역은 변수(`{{...}}`) 지정이 불가한 정적 필드라 적용 대상이 아님 (사용자 결정 2026-06-15).
- 신규 파일(저장소 캐논 참고용으로 보존): `agent_system_prompt/{EN,JP,KR}/v.0.1.4_system_prompt_*_0615.md`
- 통합 내용: ① CSV 컬럼 사전 = 서비스의 **qf/pf/cf 3제품 구조** 채택(KR엔 신규 추가) ② cf `c`=Cluster ID·`h`=Hub Keyword Flag = **repo 정의로 교정**(서비스 부정확) ③ `v`/volume = **월 평균 검색량**(데이터 사전 이미지로 확정) ④ KR **아코디언/One-Line 포맷 규칙 블록 보존**(서비스 KR엔 없던 것)
- 배포 시 v.0.1.4 본문(선두 버전 주석 제외)을 패널에 반영하면 됨. 반영 후 inService 미러 갱신 권장.
- 확인 잔여: qf의 `volume_total` UI 라벨이 사전 이미지엔 "연 평균 검색량"인데 목적 설명은 "총 검색량 합계" — 통합본은 "총 검색량 합계"로 기재. 라벨 의도 확인 필요 시 조정.

---

## 판정 범례
- 🟢 **일치** — 정규화 후 지시 내용이 실질 동일
- 🟡 **경미한 차이** — 문구·플레이스홀더·출력형식 차이, 동작 영향 거의 없음
- 🔴 **중대한 차이** — 규칙 추가/삭제·출력포맷·수치 변경 등 동작에 영향
- ⚪ **서비스에 없음** — 패널에 해당 입력이 없음

---

## 요약 (agent × locale)

| # | agent | EN | JP | KR |
|---|---|---|---|---|
| 1 | agent_ad_message_builder | 🟢 | 🟢 | 🟢 |
| 2 | agent_cluster | 🟢 | 🟢 | 🟢 |
| 3 | agent_cluster_multiple | 🟢 | 🟢 | 🟢 |
| 4 | agent_cluster_selected | 🟢 | 🟡 | 🟢 |
| 5 | agent_keyword_compare_path | 🟢 | 🟢 | 🟢 |
| 6 | **agent_past_current_cluster** | 🔴 | 🔴 | 🔴 |
| 7 | **agent_past_current_path** | ⚪ | 🔴 | 🔴 |
| 8 | agent_path | 🟢 | 🟢 | 🟢 |
| 9 | agent_persona_specialist | 🟢 | 🟡 | 🟢 |
| 10 | agent_query | 🟡 | 🟡 | 🟡 |
| 11 | **agent_system_prompt** | 🔴 | 🔴 | 🔴 |
| 12 | agent_geo_prompt_builder | 🟡 | 🟡 | 🟡 |

> ⏭️ `ai_optimizer/*` 6개 sub-agent는 빈 파일(패널에 없음으로 판단) → 비교 제외. 패널에 실제 있으면 알려주세요.

### 한눈에 보는 결론
- **🔴 중대(우선 조치): 3개 agent** — `past_current_cluster`, `past_current_path`, `system_prompt`
- **🟡 경미: 4개 agent** — `cluster_selected(JP)`, `persona_specialist(JP)`, `query(전체)`, `geo_prompt_builder(전체)`
- **🟢 일치: 5개 agent** — `ad_message_builder`, `cluster`, `cluster_multiple`, `keyword_compare_path`, `path`
- **반복 테마**: 플레이스홀더 키 불일치 `{{cluster_csv}}`(서비스) vs `{{context_csv}}`(repo)가 geo 전체·persona JP·cluster_selected JP에서 공통 발생.

---

## 버전 최신성 판정 (서비스 반영일 대조)

> repo의 git/파일 mtime은 전부 2026-06-15(오늘 Drive 동기화·커밋)이라 무의미. repo 쪽 날짜 신호는 **파일명 날짜(MMDD = 해당 버전 작성일)**뿐. 아래는 사용자 제공 서비스 반영일과 대조한 결과.

| agent | repo 최신(버전·작성일) | 서비스 반영일 | 콘텐츠 차이 방향 | 판정 |
|---|---|---|---|---|
| cluster_selected | JP v.0.1.7 (03-10) | 2026-05-20 | 서비스 JP가 **구형**(v.0.1.6급), repo가 v.0.1.7 | ⚠️ **repo 앞섬 / 서비스 미배포** |
| past_current_cluster | v.0.1.8 (05-19) | 2026-05-20 | 서비스 **구형**(§0 누락), EN은 타 에이전트 | ⚠️ **repo 앞섬 / 배포누락·오등록** |
| past_current_path | v.0.1.8 (05-19) | 2026-05-19 17:20 | 서비스 **구형**(§0 누락) | ⚠️ **repo 앞섬 / 배포누락** |
| persona_specialist | JP v.0.2.3 (03-10) | 2026-05-20 | 본문 동일, JP 플레이스홀더만 | ✅ 동일(서비스가 운영 최신) |
| query | v.0.4.5 (02-20) | 2026-05-20 | §4 출력: 서비스=인라인, repo=중첩불릿 | 🔄 **서비스 앞섬 / repo 갱신 필요** |
| system_prompt | v.0.1.3 (02-20) | 2026-05-04 | CSV 사전: 서비스 풍부 / KR 아코디언규칙: repo만 | 🔀 **혼재(양방향)** |
| geo_prompt_builder | KR v.0.3.4 (02-28) | 2026-05-04 | 플레이스홀더 키만 | ✅ 동일(서비스가 운영 최신) |

### 해석 — 두 갈래로 갈린다
- **그룹 A · repo가 앞서는데 서비스에 배포가 안 됨** (`cluster_selected JP`, `past_current_cluster`, `past_current_path`): 서비스 반영일이 더 늦은데도(=관리자 패널이 더 최근에 저장됨) 내용은 repo의 신버전(v.0.1.7/v.0.1.8)보다 **구형**. → repo에서 만든 신버전이 **패널에 적용되지 않은 상태**로 추정. 특히 `past_current_cluster`의 **EN은 Path Finder가 잘못 등록**. → 방향: **repo → 서비스 배포**.
- **그룹 B · 서비스가 운영 최신, repo가 옛 스냅샷** (`query`, `geo`, `persona`, `system_prompt`의 CSV 사전): repo 최신 버전 작성일이 2~3개월 전(02~03월)인데 서비스는 5월에 갱신됨. repo에 더 새 버전이 없으므로 **서비스가 현행 정본**. → 방향: **서비스 → repo 역반영(저장소 갱신)**.
- **혼재 · `system_prompt`**: CSV 컬럼 사전은 서비스가 최신(B), 출력 포맷(KR 아코디언 규칙)은 repo만 보유(A). 단순 한 방향 동기화 불가 — 두 최신 요소를 합쳐야 함.

---

## 🔴 중대한 차이 (우선 조치 필요)

### 6. agent_past_current_cluster — 세 로케일 모두 구버전/오등록
repo는 **v.0.1.8**(과거↔현재 검색의도 변화 비교, anti-hallucination 강화판)인데 서비스는 그 이전 로직.
- **EN 🔴**: 서비스 EN에 **아예 다른 에이전트(Path Finder, DFS/그래프 경로 탐색)**가 들어가 있음 → 패널 입력 오등록 의심.
- **JP/KR 🔴**: 구버전 공통 누락
  - **§0 Hard Anchors 블록 전체 누락** (좌=Past/우=Current 고정 매핑, `period` 컬럼 시점 식별, 클러스터 ID 출력 금지, 방향성 클리셰 데이터검증 전 금지, Previous Conversation 재산출 규칙)
  - 서비스는 `:c[클러스터ID]{#ID}` 사용, repo는 클러스터 ID 출력 **금지**(숫자 `id` 그룹핑으로 대체) — 정반대
  - 데이터 소스: 서비스 `path_persona_compare_data.past/current`(+Hub Keyword) vs repo `cluster_compare_csv`의 `period` 행
  - §4: 서비스는 "정보습득(Past)→구매(Current)" 패턴 도출을 **권장**, repo는 카운트-우선 (a)(b)(c)→방향→Rising/Falling/Sustaining 라벨로 교체하고 그 클리셰를 **금지**

### 7. agent_past_current_path — JP/KR 구버전, EN 없음
- **EN ⚪**: 패널에 입력창 없음.
- **JP/KR 🔴**: repo **v.0.1.8** 대비 구버전. 주요 누락:
  1. `# 0) 데이터/UI 기준 (Hard Anchors)` 섹션 전체 부재(좌우-시점 매핑, `period` 식별, 클러스터 ID 금지, 방향성 클리셰 금지, Previous Conversation 재산출)
  2. 데이터 모델: 서비스 `path_compare_data.past/.current`·`edges/neighbor` 구 모델 vs repo CSV `period` 행·`outgoing/id`
  3. 그래프 구축 단계 누락
  4. 카운트 선행 비교 요건 누락
  5. 출력: Past/Current 경로 구분·경로길이 비교·Rising/Falling/Sustaining 라벨 전무
  - KR-고유: §구조 규칙이 `:::accordion` **내부**로 적혀 repo(외부)와 반대(JP 서비스는 외부로 정확).

### 11. agent_system_prompt — CSV 컬럼 사전 + 포맷 규칙 불일치
- **공통 일치 부분**: Response Guidelines, System Instruction(Core Logic·Knowledge Map·5 Skill·Prohibition), Optimization, Number Abbreviation, Bitmask 디코딩(i/g/a/f).
- **EN 🔴**: **CSV Column Description**이 서비스=qf/pf/cf 3제품 상세 컬럼 사전 vs repo=단일 cf형 목록. 컬럼 의미도 불일치(`c`: 서비스 "Cluster Distribution Info" vs repo "Cluster ID", `h`: "Hub Keyword" vs "Hub Keyword Flag"). → 서비스가 더 풍부(qf/pf 해석력 우위).
- **JP 🔴**: EN과 동일 CSV 차이 + 서비스에만 있는 `[重要]` 빈 줄 2행 규칙(포맷 규칙 추가).
- **KR 🔴**: 서비스 KR에 repo의 **`아코디언 파싱 규칙 및 마크다운 최적화` 블록(One-Line Rule·코드블록 금지·➊➋➌ 문단 규칙)이 통째로 없음**(구버전 "번호 매기기/대화체" 규칙만 보유). 단 CSV 섹션은 KR끼리 일치.

> ⚠️ system_prompt는 **어느 쪽이 정본인지 방향 결정 필요**: CSV 사전은 서비스가 최신(qf/pf/cf), 출력 포맷 규칙은 repo가 최신(아코디언/One-Line). 단순 한쪽 덮어쓰기 불가.

---

## 🟡 경미한 차이

### 4. agent_cluster_selected (JP만)
서비스 JP는 v.0.1.6급, repo는 **v.0.1.7**(0310). 섹션 헤더 스타일(`# 1)` vs `# ⒈ サマリー`), 불릿 마커(`-`→`・`), 아코디언 title(`概要確認`→`すべてを見る`), `Top 3`→`トップ３`, 플레이스홀더(`{{cluster_csv}}`→`{{context_csv}}`). **실질 규칙·로직·수치는 동일**. EN/KR은 🟢 일치.

### 9. agent_persona_specialist (JP만)
본문 전부 동일. 차이는 스캐폴딩 플레이스홀더 키뿐: 서비스 `{{cluster_csv}}`(라벨 `cluster_csv:`) vs repo `{{context_csv}}`. (참고: KR은 EN/JP보다 콘텐츠가 짧아 `g/a=0 시 성별·연령 미지정` anti-hallucination 문장이 빠져 있으나, 이는 서비스·repo **양쪽 KR 공통** 격차로 drift 아님.)

### 10. agent_query (EN/JP/KR)
역할·정책·파싱규칙·의도 8종·임계값(상위 1,000개, `ads_metrics.volume_avg`, 600/800자) 모두 일치. 차이:
- **EN/KR**: §4) 인사이트 Output Format이 서비스=인라인 `**➊ 제목**: 설명` vs repo=`**➊ 제목**` 줄바꿈 후 `   - 설명` 하위불릿.
- **JP**: §4는 양쪽 동일(하위불릿), 차이는 스캐폴딩 플레이스홀더(`{{query_csv}}`+대화이력 vs repo `{{context_csv}}`만).

### 12. agent_geo_prompt_builder (EN/JP/KR)
원칙 1~9, 수치(B2B 50~120 / B2C 50~80 단어, 3~10개 생성), SAMPLE_DATA, 아코디언 규칙 모두 byte-equal. **유일한 차이는 5행 `context_data` 플레이스홀더 키**: 서비스 `{{cluster_csv}}` vs repo `{{context_csv}}`. (JP repo는 v.0.3.5로 신규지만 실질 차이 없음.)

---

## 🟢 일치 (조치 불필요)

| agent | 비고 |
|---|---|
| agent_ad_message_builder | 본문 100% 동일(공통 오타까지 동일 → 동일 소스) |
| agent_cluster | EN/KR v.0.7.2, JP v.0.7.4, 서비스=repo 동일 |
| agent_cluster_multiple | EN/KR v.0.1.0, JP v.0.1.2, 동일 |
| agent_keyword_compare_path | 본문 byte-equal, 스캐폴딩 플레이스홀더명만 차이(`context_csv`→`path_keyword_compare_csv`) |
| agent_path | EN/KR v.0.4.9, JP v.0.4.10, diff 0건 |

---

## 권장 후속 조치 (방향성 반영)

**그룹 A — repo → 서비스 배포** (repo가 앞서는데 패널 미적용)
1. **🔴 past_current_path (JP/KR)**: 서비스 패널 입력을 repo **v.0.1.8**로 교체(§0 Hard Anchors·그래프 구축·카운트 선행·Past/Current 경로 출력 반영).
2. **🔴 past_current_cluster (JP/KR)**: 동일하게 repo **v.0.1.8**로 교체. **EN 입력은 Path Finder가 잘못 등록**돼 있으니 패널에서 올바른 에이전트로 재등록 먼저.
3. **🟡 cluster_selected (JP)**: 서비스를 repo **v.0.1.7**로 갱신(헤더 `# ⒈`·불릿 `・`·title `すべてを見る`).

**그룹 B — 서비스 → repo 역반영** (서비스가 현행 정본, 저장소가 옛 스냅샷)
4. **🟡 query (EN/KR)**: repo의 §4 출력형식(중첩불릿)을 서비스의 **인라인 `**➊ 제목**: 설명`** 형태로 갱신.
5. **🟡 geo_prompt_builder / persona_specialist**: 본문은 동일, 플레이스홀더 키만 → repo를 서비스 운영본 기준으로 정리.

**혼재 — 통합 필요**
6. **🔴 system_prompt**: 단일 방향 불가. **CSV 컬럼 사전(서비스 qf/pf/cf 최신) + KR 아코디언/One-Line 포맷 규칙(repo만 보유)**을 합친 통합본을 만들어 repo·서비스 양쪽 동기화.

**공통 — 플레이스홀더 키는 "통일"이 아니라 agent별 런타임 주입 키에 맞춰야 함** (사용자 런타임 확인 결과)
7. 키 명칭은 agent마다 런타임이 실제 주입하는 값이 정답:
   - `cluster_selected` / `persona_specialist`: 런타임 = **`cluster_csv`** → repo JP의 `{{context_csv}}`가 오류였음(수정 완료).
   - `geo_prompt_builder`: 런타임 = **`context_csv`**(컬럼 id,n,v,cpc,cmp,lc,hc,vt,mv,i,f,g,a,o,c,h + top_frequency_urls + high_volume_keywords + keyword + prev_q/prev_a + user_question) → repo가 이미 올바랐고, **서비스의 `{{cluster_csv}}`가 오류**였음(서비스 3개 파일 수정 완료).
   - 따라서 "한쪽으로 표준화"가 아니라 **각 agent의 런타임 키와 프롬프트를 일치**시키는 것이 핵심.
