# PharmOS Design Prototype v2 — Change Audit

기준: 현재 PharmOS `main` 구현 vs Design Battalion prototype v2 (`pharmos-design-prototype/index.html`).

판정:
- **KEEP**: 기존 의도를 보존하면서 근거가 충분한 변경
- **REWORK**: 문제의식은 타당하나 구현 방식이 과도하거나 검증 부족
- **ROLL BACK**: 기존 의도를 깨는데 이를 이길 근거가 없음
- **EXPERIMENT ONLY**: 비교용 시안으로만 유지

## A. 모바일 Workboard / 업무 타일

| ID | 기존 PharmOS | v2 변경 | 제가 바꾼 이유 | 근거 | 근거 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-A01 | 모바일 업무판은 최종적으로 **3열 × 3행(3×3), 한 화면 최대 9개 타일**. 코드 주석에 V0.12.2→.3→.4→.5→.6까지 반복 보정 후 `FINAL: true compact 3 x 3 board` 명시. | **1열 × N 세로 목록**으로 변경. | 각 업무의 상태·설명·행동을 크게 읽게 하려는 목적. | 일반적인 가독성 논리 + DES-PH-004 작은 글자 위험. 그러나 PharmOS-specific 3×3 glanceability를 이겨야 할 증거 없음. | **부족** | **ROLL BACK** |
| PH-A02 | 타일 자체를 먼저 탭하고, 선택된 카드의 **액션 패널을 해당 3개 카드 행 바로 아래에 표시**. 코드 docstring에 “never at the top… never in separate page/dialog”를 UX invariant로 명시. | 각 업무 행에 행동 버튼을 **상시 노출**. | 한 번 덜 눌러 즉시 실행시키려는 목적. | 즉시성은 PharmOS 목표와 맞지만, 기존은 시각 스캔 밀도를 유지하기 위해 액션을 2단계로 둔 명시적 invariant가 있음. | **부족** | **ROLL BACK / REWORK** |
| PH-A03 | 타일은 고정 높이 약 **5.15rem**, 제목 최대 4줄 clamp. 3개가 한 행에 들어가도록 폭 강제. | 업무 본문을 13px 제목 + 11px 메타 + 상태 pill + 버튼으로 확장. | 작은 타일 글씨 문제 해결. | 가독성 문제는 확인됨. 다만 전체 구조를 바꿀 필요는 검증 안 됨. | **부분** | **REWORK** — 3×3 유지 + 선택 상세영역 개선 |
| PH-A04 | 타일은 **흰색 바탕 / #101828 텍스트 / 얕은 그림자**. V0.12.2에서 “no gradient-on-dark-text failure”를 명시. | 업무를 흰 리스트 surface에 넣고 상태 dot/pill을 추가. | 시각적 우선순위 표현. | 흰 바탕 유지 자체는 현행과 합치. dot/pill 추가 근거는 별도 없음. | **부분** | 흰 바탕 **KEEP**, dot/pill **EXPERIMENT ONLY** |
| PH-A05 | 지연/오늘/임박/예정/완료는 섹션 헤더 색으로 구분. | 상단 chip `지금/오늘/지연/예정/완료`로 재구성. | 한 줄 필터로 상태 전환을 빠르게 하려는 목적. | 기존 상태 분류는 근거 있음. 그러나 `지금`이라는 새 범주와 chip UI는 v2 임의 해석. | **부분** | **REWORK** |
| PH-A06 | 모바일 업무판은 fragment rerun으로 액션 후 전체 앱 재실행을 피함. | 정적 JS에서 즉시 UI 갱신/토스트. | 시제품 상호작용을 보여주기 위함. | 구현기술 차이일 뿐 디자인 근거 아님. | N/A | **EXPERIMENT ONLY** |
| PH-A07 | `완료 / 누락 / 상세·수정`은 선택된 업무 액션 패널의 핵심 상태전이. `누락`은 기한 조건에 따라 disable. | 업무마다 `확인 시작/보기`, `재고 보기/7품목만`, `계속 작성/완료 처리` 등 **도메인별 커스텀 버튼**으로 변경. | generic CRUD보다 업무 의미를 직접 표현하려는 목적. | Domain Familiarity/직접행동 원칙에는 맞지만 실제 command/state contract와 1:1 검증 안 됨. | **부분** | **REWORK** |
| PH-A08 | 완료 후 optimistic completion/flash 등 기존 상태 처리 존재. | 완료 직후 inline `되돌리기` 노출. | recovery를 눈앞에 두려는 목적. | 기존 설계의 undo/부분복원 철학과 맞음. 하지만 모든 mutation에 universal undo가 있는 것은 아님. | **부분** | **KEEP only where backend supports undo** |
| PH-A09 | 기존 업무판에서 여러 업무를 **공간적으로 스캔**하는 것이 핵심. | 한 업무가 수직 공간을 크게 차지. | 설명력 우선. | 기존 3×3 반복 개선 자체가 반대 증거. | **부족** | **ROLL BACK** |

## B. 색상 / 표면 / 그림자 / 모서리

| ID | 기존 PharmOS | v2 변경 | 제가 바꾼 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-B01 | 모바일 배경 `--m-bg:#f3f6ff`, 좌상단 blue radial gradient 포함. | `--bg:#f5f7f8` 단색 회백색. | “calm professional” 분위기와 정보 집중. | Design doctrine의 calm은 있으나 **현재 PharmOS blue/purple 시각정체성을 버릴 근거 없음**. | **부족** | **ROLL BACK / PALETTE TEST 필요** |
| PH-B02 | 모바일 브랜드 `#315efb` + `#7c4dff` gradient. | navy `#15324a` + teal `#0c8b80` 중심. | 의료/운영 도구의 안정감·차분함을 주려는 임의 선택. | 외부 레퍼런스 수준의 디자인 추론일 뿐 PharmOS 사용자검증 없음. | **부족** | **EXPERIMENT ONLY** |
| PH-B03 | Desktop/global accent `#315efb`, success `#087a55`, danger `#c4322b`, warning `#b54708`. | blue `#3e72c9`, teal `#0c8b80`, red `#c84e4e`, amber `#b97b22`. | 채도 완화 및 teal을 primary action으로 사용. | 기존 의미색 체계가 이미 존재. 바꿀 이유 검증 안 됨. | **부족** | **ROLL BACK** |
| PH-B04 | 하단 nav active = blue→purple gradient + 흰 글씨. | active = teal 단색 텍스트/아이콘, 배경 없음. | 더 조용한 navigation을 만들려는 목적. | 현행은 active affordance를 강하게 주기 위한 명시적 스타일. 약화 근거 없음. | **부족** | **ROLL BACK** |
| PH-B05 | 하단 nav 자체 = 반투명 흰색 + blur 22px + 큰 shadow + radius 21~23px, 화면 가장자리에서 떠 있는 형태. | 완전 흰색, 상단 border 1px, 화면 전체 폭에 붙인 전통 bottom bar. | 구조적 안정성/단순화. | 현행 floating nav를 버릴 근거 없음. | **부족** | **ROLL BACK** |
| PH-B06 | 모바일 일반 버튼 radius 약 **15px**, min-height **3rem**, font-weight 820. | 일반 actions radius 9~10px, 38~42px 높이, weight 700. | 더 “업무도구” 같은 compact 느낌. | 오히려 기존은 터치 접근성과 큰 조작감이 의도됨. | **반대 근거 강함** | **ROLL BACK** |
| PH-B07 | menu card radius 20px, task tile radius 약 14px, metric radius 18px. | surface radius 16px, action 9px, role 11px 등 전체적으로 각을 줄임. | 시각적 성숙도/밀도 조절이라는 임의 판단. | 근거 없음. | **없음** | **EXPERIMENT ONLY** |
| PH-B08 | task tile shadow `0 2px 8px rgba(16,24,40,.045)` 최종. menu/nav는 더 큰 shadow. | 대부분 border 중심, shadow 최소화. | 시각적 잡음 감소. | 현행도 task tile shadow는 이미 약함. 추가 이득 미검증. | **낮음** | **ROLL BACK 또는 동일 유지** |
| PH-B09 | 모바일 화면에 blue/purple gradient를 일부 사용해 active/brand/navigation 위계 표현. | gradient 거의 제거. | “calm”을 위해서. | 브랜드/상태 위계 손실 가능. 검증 없음. | **부족** | **ROLL BACK** |

## C. 글꼴 / 글자 크기 / 정렬 / 정보밀도

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-C01 | system stack: Apple/Segoe UI/Noto Sans KR 계열. | Apple/Noto Sans KR/Apple SD Gothic Neo 계열. | 플랫폼 기본 폰트 유지. | 현행과 사실상 동일 철학. | 충분 | **KEEP** |
| PH-C02 | 모바일 brand strong 1.05rem, page h1 1.45rem. | brand 20px, home headline 26px. | 첫 화면 제목 가독성 강화. | 작은 task text 위험과 별개로 큰 heading 확대 근거는 약함. | 부분 | **EXPERIMENT ONLY** |
| PH-C03 | task tile text 최종 약 0.62rem, 4줄 clamp. | work title 14px, meta 11px. | DES-PH-004 가독성 문제 대응. | 실제 위험후보와 직접 연결됨. | 비교적 강함 | **KEEP principle / not 1×N implementation** |
| PH-C04 | mobile tile은 중앙 정렬·공간 기억에 유리. | 업무 리스트는 좌측 정렬. | 문장/메타 읽기 편하게. | 1×N일 때 자연스러운 선택이나 3×3을 유지하면 전제가 사라짐. | 전제 의존 | **ROLL BACK with grid** |
| PH-C05 | 모바일 타일은 중앙 정렬. | 상태 pill을 우측, 제목/메타를 좌측에 배치. | scan path를 좌→우로 만들기 위함. | 사용자 검증 없음. | 부족 | **EXPERIMENT ONLY** |
| PH-C06 | 기존 모바일 타일은 작은 화면에서도 3개를 한 행에 넣기 위해 380px 이하 font 0.52~0.59rem까지 축소하는 역사 있음. | 최소 글자 크기를 상대적으로 크게 유지. | readability. | 이 문제 자체는 실증 코드로 확인됨. | 강함 | **문제 정의 KEEP, 해결책 REWORK** |

## D. Header / 역할 / 자연어 입력

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-D01 | 모바일 brand + 현재 page chip이 상단에 존재. | `PharmOS + Design Battalion subtitle` + 직원/약국장 toggle을 상단 고정. | role context를 항상 보이게. | role 분리는 중요하지만 toggle을 항상 노출해야 한다는 근거 없음. | 부족 | **REWORK** |
| PH-D02 | 기존 role/authorization은 actor/role에 의해 결정되며 단순 UI theme toggle이 아님. | 직원/약국장 모드를 사용자가 버튼으로 즉시 전환. | 시제품에서 두 IA를 한 파일로 보여주기 위한 데모 장치. | 실제 제품에서는 권한모델과 혼동. | 명확히 데모용 | **EXPERIMENT ONLY / production 금지** |
| PH-D03 | 음성/자연어는 기존 command interpreter + confirmation gate + canonical execution pipeline. | 상단에 텍스트 command bar를 항상 노출. | 자연어를 전역 accelerator로 보여주려는 목적. | 자연어가 전역 접근경로라는 제품 철학은 강함. 하지만 항상 상단 고정은 미검증. | 부분 | **REWORK** |
| PH-D04 | 기존 모바일은 voice recorder/화면별 액션과 command flow가 공존. | `⌘` 아이콘 + input + 실행 버튼으로 단순화. | backend 없이 시제품으로 command concept 표현. | 구현 축약일 뿐 실제 UI 정답 아님. | 낮음 | **EXPERIMENT ONLY** |
| PH-D05 | confirmation은 command 위험도/상태에 따라 동적. | 일부 버튼에 browser confirm/modal을 고정. | irreversible action 구분을 시각화. | 원칙은 맞지만 command contract와 미연결. | 부분 | **REWORK** |

## E. Bottom navigation / IA

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-E01 | 모바일 bottom nav 5칸 floating, active gradient. 내부 More/페이지 구조 존재. | `오늘/재고/근무/보고/관리` 5칸 고정. | 핵심 workflow를 바로 노출. | 현행 실제 mobile IA와 1:1 대조 없이 임의 선정. | 부족 | **ROLL BACK / 현행 IA 기준 재설계** |
| PH-E02 | 관리기능은 기존 역할/사이드바/더보기 체계와 연결. | 약국장 모드에서 `관리` 탭 자체를 추가. | owner 기능 분리. | role split 철학은 맞으나 실제 nav 위치 근거 없음. | 부분 | **REWORK** |
| PH-E03 | 더보기에는 2열 menu card 등 저빈도 기능을 수용. | v2에서는 More 개념을 사실상 제거. | 메뉴층 단순화. | **기존 저빈도 기능 수용장치를 없앤 것**이라 기능 확장성 손실. | 반대 근거 있음 | **ROLL BACK** |

## F. Home / Today 구조

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-F01 | 핵심은 오늘 workboard와 task states. | `TODAY WORKBOARD / 오늘 약국에서 해야 할 일`을 더 강하게 전면화. | 원래 제품의 daily operations 핵심을 명확히. | 기존 제품 목표와 직접 일치. | 강함 | **KEEP concept** |
| PH-F02 | 현행 상태/업무 중심. | `방금 하던 일` resume card를 최상단 별도 surface로 추가. | interruption recovery 외재화. | 디자인 doctrine/부분복원 철학은 있으나 현재 실제 resume state 구현 범위는 별도 확인 필요. | 부분 | **REWORK / 실제 state 있을 때만** |
| PH-F03 | quick actions는 현행 모바일에도 존재. | `재고 실사/보고 작성/휴가·근무/업무 이력` 4칸 quick row. | 자주 쓰는 기능 직접 접근. | quick action 원칙은 현행과 맞음. 정확한 4개 선정은 검증 부족. | 부분 | **KEEP structure, REVALIDATE contents** |
| PH-F04 | 모바일 quick/menu 카드가 둥근 2열 또는 별도 grid. | 4개를 한 줄 `repeat(4,1fr)`로 축소. | 한 화면 밀도. | 터치/글자 크기 저하 가능, 현행 2열 카드 의도와 충돌. | 부족 | **ROLL BACK / REWORK** |
| PH-F05 | 기존은 task가 직접 중심. | 별도 `오늘 상태` 2×2 KPI를 추가. | 업무 외 전체 상황 awareness. | 현행 metric/상태 개념은 있으나 이 4개 지표 선정 근거 없음. | 낮음 | **EXPERIMENT ONLY** |

## G. 재고 화면

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-G01 | PM20 parser, LOCAL/PM20 source, discrepancy/reconciliation 등 실제 모델 존재. | 상단 설명에 `PM20 원본은 건드리지 않고 PharmOS에서 실사·불일치·반영 준비`를 명시. | 기존 architecture boundary를 UI에 설명. | 실제 기능/보호원칙과 부합. | 강함 | **KEEP copy principle** |
| PH-G02 | 실제 inventory UI 구조는 기존 app의 session/search/discrepancy/reconciliation 흐름. | `실사/불일치/입고/반영 준비` subnav를 새로 구성. | specialist workflow로 분리. | 실제 current page IA와 정밀 대조 없이 임의 분류. | 부족 | **REWORK** |
| PH-G03 | 실제 quantity parsing에 pack/direct/calculated/final quantity 등 세부 로직 존재. | 단순 +/- counter 하나로 축약. | 모바일 demo 간소화. | 실제 재고 업무 의미를 손실. | 부족 | **ROLL BACK as product design** |
| PH-G04 | discrepancy/reconciliation 후보와 조회 command가 실제 존재. | 3개 샘플 discrepancy row + delta 색상. | workflow 시각화. | 개념은 맞지만 정보필드/행동이 실제 모델보다 과도하게 단순. | 부분 | **REWORK** |

## H. 근무·휴가 / 보고 / 관리 화면

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-H01 | leave balance, schedule, owner manual events, substitute work 등 실제 복잡한 모델. | 주간 3행 + 휴가잔액 + 승인대기로 단순화. | 모바일 핵심만 보이게. | 실제 업무 모델을 충분히 반영하지 못함. | 부족 | **ROLL BACK / REWORK from actual model** |
| PH-H02 | 보고는 audience/visibility confirmation(`전 직원 공개` vs owner-only 등)과 command confirmation 흐름 존재. | textarea + 단순 등록 중심으로 축약. | prototype simplicity. | 중요한 visibility/confirmation 의미 손실. | 부족 | **ROLL BACK** |
| PH-H03 | 기존 voice report confirmation에서 3-column choice UI가 존재. | 보고 visibility UI를 거의 제거. | 복잡도 감소. | 안전/의도 확인을 희생. | 반대 근거 있음 | **ROLL BACK** |
| PH-H04 | 관리 화면은 staff/admin/AI learning/backup 등 여러 실제 영역. | tax/management 중심으로 묶음. | owner mental model 단순화. | 실제 navigation/권한/기능범위를 대표하지 못함. | 부족 | **ROLL BACK / scope 재정의** |

## I. Desktop adaptation

| ID | 기존 PharmOS | v2 변경 | 이유 | 근거 | 충분성 | 판정 |
|---|---|---|---|---|---|---|
| PH-I01 | Streamlit desktop sidebar radio/navigation + wide layout. | 235px deep-navy custom sidebar. | prototype에서 desktop 전문도구 느낌. | 현행 sidebar를 대체할 근거 없음. | 부족 | **EXPERIMENT ONLY** |
| PH-I02 | desktop task cards 4열 grid (`column_count=4`). | desktop에서도 home workitems가 사실상 1열 리스트. | 모바일/desktop 개념 통일. | desktop의 넓은 공간 활용을 저해. | 부족 | **ROLL BACK** |
| PH-I03 | desktop/mobile paths가 별도 검증 대상. | CSS breakpoint 900px 하나로 단순 전환. | prototype 구현 단순화. | 실제 제품 breakpoint/Streamlit behavior 근거 아님. | 없음 | **EXPERIMENT ONLY** |

## J. 총괄 판정

### 즉시 철회해야 할 v2 변경
1. 모바일 **3×3 업무판 → 1×N 리스트**
2. 선택 후 local-in-place action → **상시 action 버튼 노출**
3. blue/purple 현행 palette → **navy/teal로 전면 교체**
4. floating glass bottom nav → **평면 full-width bottom bar**
5. 기존 큰 터치버튼 radius/높이 → **더 작고 각진 버튼**
6. More/저빈도 수용 구조 제거
7. 재고/근무/보고의 실제 workflow를 generic simplified screen으로 축약
8. desktop 4열 task grid → 1열 리스트

### 유지할 문제의식, 구현은 다시 해야 하는 것
1. 작은 모바일 task text의 가독성 개선
2. interruption/recovery를 더 명확하게 보여주기
3. 자연어를 전역 accelerator로 취급
4. 오늘 업무 중심의 hierarchy 강화
5. undo/recovery를 backend가 실제 지원하는 mutation에서 더 잘 노출
6. PM20/LOCAL boundary를 사용자에게 더 명확히 전달

### 현재 v2에서 그대로 유지 가능한 것
- system/native Korean font stack 유지
- Today Work 중심이라는 제품 framing
- 실제 아키텍처 경계를 설명하려는 inventory copy의 방향

## 다음 시제품 규칙

다음 v3는 **현행 PharmOS UI를 기준점으로 고정**한다.
- 3×3 모바일 task board 유지
- 3×3의 공간 기억/한눈 스캔을 보존
- 선택한 타일의 액션은 그 행 바로 아래에 붙는 invariant 유지
- 기존 blue/purple palette를 기준으로 시작
- 기존 floating bottom nav/More IA를 기준으로 시작
- 폰트/버튼/spacing은 현행값을 먼저 복제한 뒤, 바꿀 때만 이 문서에 변경사유를 선기록
- 실제 기능 workflow와 state contract를 확인하지 않은 새 버튼/상태/메뉴를 만들지 않음
