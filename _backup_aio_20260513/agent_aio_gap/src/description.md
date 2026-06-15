## 요청 사항

- Gap Analysis 프롬프트를 만들고자 합니다. 타 ai_agents에서 사용되는 양식을 참고하여, 생성하세요.
- 기존 콘텐츠 가이드에 대한 정의가 필요하다면, https://notebooklm.google.com/notebook/b5ad73ea-0078-413a-9a2e-612ed5da3621 참고
- 해당 작업이 요청된 자료가 필요하다면 https://notebooklm.google.com/notebook/2643c969-0f4f-4b67-886d-4630fdfdf6ed 참고

**Gap Analysis의 목표와 방법**

**목표**

내 콘텐츠가 AI 응답에 제대로 반영되지 못하는 이유를 찾아내는 것. 구체적으로는, 사용자 질문 의도(CEP)에 대해 AI가 만든 답변과 내 페이지 콘텐츠를 비교해서 **내 페이지에 빠져 있는 의미적 영역**을 도출하는 것이 목표입니다. "잘하고 있는 부분"을 평가하는 게 아니라, 오직 **누락된 내용**만 파악하는 데 초점을 둡니다.

이 결과는 그 자체로 끝이 아니라, 이후 온드미디어·언드미디어·샘플 작성 에이전트가 콘텐츠 전략을 재구성할 때 사용하는 **입력값**이 됩니다.

**방법**

비교 대상은 ABC 프레임으로 정의됩니다.

1. **A**: 내 페이지를 파싱한 콘텐츠 agents/ai*agent/agent_aio_gap/src/A*가이드에 사용하는 내 페이지를 파싱한 콘텐츠(콘텐츠 가이드에만 활용) .md
2. **B**: CEP별로 만든 프롬프트 /Users/ascentkorea/Library/CloudStorage/GoogleDrive-hokyun.moon@ascentnet.co.jp/내 드라이브/workspace_ascent/202601_aiAgent/promptMarkdown/agents/ai_agent/agent_aio_gap/src/B_AI에 던지는 질문 (CEP별로 만든 프롬프트).md
3. **C**: 그 프롬프트로 받은 AI 응답
   - /Users/ascentkorea/Library/CloudStorage/GoogleDrive-hokyun.moon@ascentnet.co.jp/내 드라이브/workspace_ascent/202601_aiAgent/promptMarkdown/agents/ai_agent/agent_aio_gap/src/C_B프롬프트로 받은 AI 응답\_1.md
   - /Users/ascentkorea/Library/CloudStorage/GoogleDrive-hokyun.moon@ascentnet.co.jp/내 드라이브/workspace_ascent/202601_aiAgent/promptMarkdown/agents/ai_agent/agent_aio_gap/src/C_B프롬프트로 받은 AI 응답\_2.md
   - /Users/ascentkorea/Library/CloudStorage/GoogleDrive-hokyun.moon@ascentnet.co.jp/내 드라이브/workspace_ascent/202601_aiAgent/promptMarkdown/agents/ai_agent/agent_aio_gap/src/C_B프롬프트로 받은 AI 응답\_3.md

C를 기준으로 두 가지를 본 뒤, A와 비교합니다.

- C 안에 내 브랜드가 노출됐는지, 다른 브랜드가 나왔다면 왜 나왔는지
- B(프롬프트)를 기준으로 마땅히 다뤄져야 할 내용 중, C에는 있지만 A에는 없는 영역

비교 방식은 **임베딩 기반 벡터 비교**로 전환을 검토 중입니다. 현재 베리박스 가이드처럼 LLM이 텍스트를 직접 비교하는 방식은 단어·문장 일치 수준에서만 판단하기 때문에, 의미는 같지만 표현이 다른 콘텐츠를 누락으로 잘못 잡습니다. 예를 들어 공기청정기 CEP가 "신생아용"일 때, 페이지에 "신생아"라는 단어가 없더라도 같은 맥락의 내용이 있으면 갭이 없다고 판단할 수 있어야 합니다. 이를 위해 A와 C를 각각 임베딩해 벡터 공간에서 유사도를 비교하고, 의미적으로 비어 있는 영역을 도출합니다.

옵션은 임베딩 단독, SQ 단독, 또는 둘의 조합 중 어느 쪽이 적합한지 엔지니어와 협의해 결정합니다. 보조적으로는 자사 페이지에서 SPO를 추출해 날리지 그래프를 만들고 임베딩과 함께 활용하는 방식도 시연된 바 있습니다.

도출되는 결과는 "이 문장을 넣으세요" 식의 직접 수정 지시가 아니라, **"이런 종류의 콘텐츠와 엔티티가 페이지에 있어야 한다"** 수준의 가이드입니다. 한 페이지에 통합할지 별도 페이지로 분리할지는 AI가 기존 페이지 내용을 보고 판단·제안하도록 합니다.

마지막으로, 로직 자체를 더 정교화하는 것보다 **"임베딩으로 고쳤더니 실제 AI 응답에 노출되더라"는 검증 사이클**에 시간을 쓰는 것이 더 가치 있다는 방향으로 정리됐습니다. 정교화는 추후 단계로 미루고, 우선은 뒤쪽 에이전트·결과 검증을 먼저 잡습니다.
