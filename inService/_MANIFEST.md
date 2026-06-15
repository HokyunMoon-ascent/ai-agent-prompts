# inService 매니페스트

`admins.listeningmind.com/hubble/gpt-prompt` 관리자 패널에 **현재 서비스로 적용 중인** agent 프롬프트를 모아두는 폴더입니다.
각 파일에 서비스 원문을 붙여넣은 뒤, repo 원본과 차이를 비교합니다 (→ `_COMPARISON_REPORT.md`).

## 상태 범례
- 🟡 **빈 파일** — 서비스 원문을 붙여넣어야 함
- 🟢 **스크랩 완료** — 서비스 포맷 원문이 이미 들어 있음
- ⚪ **repo복사본-비움** — 이전에 repo 파일이 잘못 복사돼 있던 것을 비움. 서비스 원문으로 다시 채워야 함

## locale-split agent (12종)

| inService 파일 | 상태 | repo 원본 (최신 버전) |
|---|---|---|
| `agent_ad_message_builder/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_ad_message_builder/{EN,JP,KR}/v.0.1.0_ad_message_builder_*_0401.md` |
| `agent_cluster/en.md` | ⚪ | `agent_cluster/EN/v.0.7.2_cf_EN_0602.md` |
| `agent_cluster/jp.md` | ⚪ | `agent_cluster/JP/v.0.7.4_cf_JP_0611.md` |
| `agent_cluster/kr.md` | ⚪ | `agent_cluster/KR/v.0.7.2_cf_KR_0602.md` |
| `agent_cluster_multiple/en.md` | ⚪ | `agent_cluster_multiple/EN/v.0.1.0_cf_m_EN_0602.md` |
| `agent_cluster_multiple/jp.md` | ⚪ | `agent_cluster_multiple/JP/v.0.1.2_cf_m_JP_0611.md` |
| `agent_cluster_multiple/kr.md` | ⚪ | `agent_cluster_multiple/KR/v.0.1.0_cf_m_KR_0602.md` |
| `agent_cluster_selected/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_cluster_selected/EN(0.1.6)·JP(0.1.7)·KR(0.1.6)` |
| `agent_keyword_compare_path/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_keyword_compare_path (키워드 비교)/{EN,JP,KR}/v.0.1.7_*_0504.md` |
| `agent_past_current_cluster/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_past_current_cluster (CF, 페르소나뷰-과거 비교)/{EN,JP,KR}/v.0.1.8_*_0519.md` |
| `agent_past_current_path/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_past_current_path (PF-과거비교)/{EN,JP,KR}/v.0.1.8_*_0519.md` |
| `agent_path/en.md` | 🟢 | `agent_path/EN/v.0.4.9_pf_EN_0519.md` |
| `agent_path/jp.md` | ⚪ | `agent_path/JP/v.0.4.10_pf_JP_0611.md` |
| `agent_path/kr.md` | 🟢 | `agent_path/KR/v.0.4.9_pf_KR_0519.md` |
| `agent_persona_specialist/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_persona_specialist/EN(0.2.2)·JP(0.2.3)·KR(0.2.2)` |
| `agent_query/en.md` | 🟢 | `agent_query/EN/v.0.4.5_if_EN_0220.md` |
| `agent_query/jp.md` | 🟢 | `agent_query/JP/v.0.4.5_if_JP_0310.md` |
| `agent_query/kr.md` | 🟢 | `agent_query/KR/v.0.4.5_if_KR_0220.md` |
| `agent_system_prompt/{en,jp,kr}.md` | 🟡🟡🟡 | `agent_system_prompt/{EN,JP,KR}/v.0.1.3_system_prompt_*_0220.md` |
| `agent_geo_prompt_builder/{en,jp,kr}.md` | 🟡🟡🟡 | `GEO_prompt_builder/EN(0.3.4)·JP(0.3.5)·KR(0.3.4)` |

## ai-optimizer sub-agent (단일 파일 구조, locale-split 아님)

> ⚠️ 이 sub-agent들이 `gpt-prompt` 관리자 패널에 실제 포함되는지 확인 필요. 미포함이면 `inService/ai_optimizer/` 폴더 삭제.

| inService 파일 | 상태 | repo 원본 |
|---|---|---|
| `ai_optimizer/agent_aiOpt_cep/prompt.md` | 🟡 | `ai-optimizer/agent_aiOpt_cep/` |
| `ai_optimizer/agent_aiOpt_earned/prompt.md` | 🟡 | `ai-optimizer/agent_aiOpt_earned/dev_request_aiOpt_earned_0504.md` |
| `ai_optimizer/agent_aiOpt_gap/prompt.md` | 🟡 | `ai-optimizer/agent_aiOpt_gap/dev_request_aiOpt_gap_0430.md` |
| `ai_optimizer/agent_aiOpt_noneURL/prompt.md` | 🟡 | `ai-optimizer/agent_aiOpt_noneURL/dev_request_aiOpt_noneURL_0504.md` |
| `ai_optimizer/agent_aiOpt_owned/prompt.md` | 🟡 | `ai-optimizer/agent_aiOpt_owned/dev_request_aiOpt_owned_0504.md` |
| `ai_optimizer/agent_ai_opt_response_analyst/prompt.md` | 🟡 | `ai-optimizer/agent_ai_opt_response_analyst (통합)/` |

## 채워야 할 체크리스트 (🟡·⚪ = 31개 파일)

- [ ] agent_ad_message_builder: en, jp, kr
- [ ] agent_cluster: en, jp, kr (⚪ repo복사본 비움 — 서비스 원문 필요)
- [ ] agent_cluster_multiple: en, jp, kr (⚪)
- [ ] agent_cluster_selected: en, jp, kr
- [ ] agent_keyword_compare_path: en, jp, kr
- [ ] agent_past_current_cluster: en, jp, kr
- [ ] agent_past_current_path: en, jp, kr
- [ ] agent_path: jp (⚪) — en/kr은 이미 완료
- [ ] agent_persona_specialist: en, jp, kr
- [ ] agent_system_prompt: en, jp, kr
- [ ] agent_geo_prompt_builder: en, jp, kr
- [ ] ai_optimizer: 패널 포함 시 6개 sub-agent

✅ 이미 완료(🟢): agent_path/en, agent_path/kr, agent_query/{en,jp,kr}

## 다음 단계

위 빈 파일에 서비스 원문을 채운 뒤 알려주시면, repo 원본과 **콘텐츠 레벨 비교**를 수행해 `_COMPARISON_REPORT.md`를 생성합니다.
