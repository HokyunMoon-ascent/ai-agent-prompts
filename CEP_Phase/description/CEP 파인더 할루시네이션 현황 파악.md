# CEP 파인더 할루시네이션 현황 파악

관련 산출물: 

- [As-Is/To-Be 비교 맵](https://claude.ai/code/artifact/f5c851d6-5a1c-450a-8a5f-54e45d5540ed)
- [Phase 3.5](https://drive.google.com/file/d/1fTIVC3Uq-1GF6DsoNEHRQ5FpxV6pRVAL/view?usp=sharing) / [Phase 4](https://drive.google.com/file/d/1-5dFCHQ-HysMfDDoTLcpZVW0qcWTg_aP/view?usp=sharing) 프롬프트 초안
- <https://hallucination-test.vercel.app/dashboard>

요약

- CEP 파인더가 만든 카드에서 할루시네이션 발생. (원본 자료에 없는 시간·장소·인용·행동 추가)
- 네 유형 각각을 생성 시점에 차단하는 방법 구상

---

## Why — 무엇이 문제인가

### 1. 배경 — CEP 파인더 할루시네이션 발생

카드가 **소스에 없는 디테일을 스스로 만들어 넣는 현상** 확인

| **구분** | **CEP가 내놓은 문장** | **URL의 원본 문장** | **찾기 검증** | **다른 점** |
| --- | --- | --- | --- | --- |
| **① 시간 단정** [**++프로젝트 1277 (노트북) · CEP 10번++**](https://release.listeningmind.com/ko/cep/1277) | 재택/원격근무가 시작된 첫 주에, 집 밖 이동도 생길 것 같아 ‘어디서든 연결되는’ 조건을 최우선으로 두고 고를 때[https://m.ppomppu.co.kr/new/bbs\_view.php?id=review2&no=60024](https://m.ppomppu.co.kr/new/bbs_view.php?id=review2&no=60024)인용 URL | "기가비트 무선랜을 통해 WiFi만 있다면 어디서든 빠른 인터넷이 가능하고" · "온라인수업, 재택근무용 혹은 휴대성 높은 노트북을 원하셨다면" | WiFi만 있다면 어디서든 → 찾아짐첫 주 · 원격 → 0건 | 사용기는 용도("재택근무용")와 조건("어디서든")만 말합니다. '시작된 첫 주'라는 시점은 페이지에 없습니다. 같은 '첫 주'가 1328(버티컬 마우스) CEP 4번에서도 발생했습니다. |
| **② 없는 장소** [**++프로젝트 1288 (가족 여행) · CEP 4번++**](https://release.listeningmind.com/ko/cep/1288) | 실내에서 활동할 때도 ‘부모 대기’가 길어지면 힘들어서 카페/대기 좌석이 있는 테마형 장소를 확인한다<https://www.i-rang.net/place/wonderpark> | "1층에 카페가 있어 간단한 음료와 간식을 이용할 수 있어요." | 1층에 카페가 있어 → 찾아짐테마형 · 대기 좌석 → 0건 | 페이지에 있는 것은 1층 카페뿐입니다. '대기 좌석'이라는 시설과 '테마형 장소'라는 성격 규정은 카드가 만들었습니다. |
| **③ 가짜 인용** [**++프로젝트 1328 (버티컬 마우스) · CEP 4번 (①과 같은 카드)++**](https://release.listeningmind.com/ko/cep/1328) | …결국 \*\*‘손목도 마우스로 더 안 맞는 것 같다’\*\*고 느껴… (따옴표로 감싸 실제 발화처럼 표기)[++인용 URL++](https://www.logitech.com/content/dam/logitech/ko/business/pdf/touchpads-vs-mice-ebook.pdf) | "대다수의 근로자는 노트북을 사용할 때 필요한 장비를 인체공학적으로 설정하지 못합니다." | 인체공학적으로 설정하지 못합니다 → 찾아짐손목도 · 더 안 맞는 → 0건 | 원문은 설문 결과를 전하는 평서문입니다. 따옴표 안의 발화는 문서 어디에도 없습니다 |
| **④ 없는 행동** [**++프로젝트 1273 (공기청정기) · CEP 6번++**](https://release.listeningmind.com/ko/cep/1273) | 미세먼지가 심한데 창문을 잘 못 열고 실내를 계속 닫고 생활해야 하는 날, ‘환기 대체’로 공기청정기를 계속 켜둘지 고민한다[++인용 URL++](https://www.a-ha.io/questions/412a623cfc29f183b6de593f7991898e) | "미세먼지가 들쑥날쑥 괜찮은거 보고 창문 열어두면 안좋아지고 다른분들은 환기 어떻게 시켜주시나요?" | 다른분들은 환기 어떻게 시켜주시나요 → 찾아짐 고민 · 켜둘지 · 환기 대체 → 0건 | 질문자는 환기 방법을 물을 뿐입니다. '켜둘지 고민한다'는 행동과 '환기 대체'라는 인식은 카드가 만들었습니다. |

### 확인된 프롬프트 이슈

|  | **위치** | **이슈 발생 추정 문구** | **이유** |
| --- | --- | --- | --- |
| 1 | Phase 3 |  | 상황이 인용 URL에서 발견되지 않아도 강제로 생성 |
| 2 | Phase 3 |  | 의역이 발생함 |
| 3 | Phase 4 |  | 앞서 만들어진 7W 요소가 강제적으로 사용되게 됨. 실제로는 없을 수도 있음. |

---

## What — 무엇을 바꾸나

**가설: **

- Why에서 확인한 문제를 일으키는 것으로 추정되는 프롬프트 개선
- **웹 조사(Phase 3) 직후에 '증거 분해' 단계(Phase 3.5)를 신설**
- **카드 생성(Phase 4)**을 활용하여 답변을 생성하면, 해당 문제를 해결할 수 있을 것이다.

| **Why에서 확인한 유형** | **차단 메커니즘 (To-Be)** |
| --- | --- |
| **7W 개선** | Phase 3의 7W **증거 유닛의 인용문에 있을 때만** 기입한다. |
| **나노 인텐트 제거** | 나노 인텐트는 Web Search 기반으로 나오는 결과가 아니므로, 제거. |
| **인용 여부** | - 원문을 가능하면 그대로 인용하도록 처리 - 카드의 행동 서술은 선택한 인용문의 표현에 대응돼야 함.    대응 없는 행동 서사 금지. |

---

## How — 어떻게 실행하나

| **산출물** | **위치** |
| --- | --- |
| **Phase 3.5 프롬프트 초안** | <https://drive.google.com/file/d/1fTIVC3Uq-1GF6DsoNEHRQ5FpxV6pRVAL/view?usp=sharing> |
| **Phase 4 개정 초안 (v1.1.0)** | [https://drive.google.com/file/d/1-5dFCHQ-HysMfDDoTLcpZVW0qcWTg\_aP/view?usp=sharing](https://drive.google.com/file/d/1-5dFCHQ-HysMfDDoTLcpZVW0qcWTg_aP/view?usp=sharing) |

### 실행 단계

| **단계** | **내용** | **판단 기준** |
| --- | --- | --- |
| **① 초안 리뷰** | Phase 3.5 / 4 초안 승인 | — |
| **② 개발팀에 요청 후 프로세스로 추가** | Phase 3.5를 실제 모델에 태워 유닛 품질 확인 | 위 사례 4건(CEP 1·4·9)이 재발하지 않는 카드가 나오는지 |
| **④ 회귀 측정** | 프로젝트 재생성 후 동일 검사기로 전후 비교 | 아래 성공 지표 |
