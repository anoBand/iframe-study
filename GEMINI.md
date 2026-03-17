<Project_Context>
사용자는 HTML iframe의 5가지 실무 활용 사례를 직접 체험할 수 있는 'Interactive iframe Showcase' 대시보드를 구축하고자 합니다. 이 프로젝트는 단순한 코드 예시 모음이 아니라, 부모-자식 간의 물리적 분리 상황에서 데이터가 어떻게 흐르고 보안이 어떻게 작동하는지 증명하는 '실동작 서비스'입니다. 모든 파일은 동일 출처(Same-origin) 환경에서 작동합니다.
</Project_Context>

<Goal>
사이드바 레이아웃을 가진 호스트(Host) 앱을 구축하고, 5가지 iframe 예제 페이지를 동적으로 로드하며 상호작용하는 시스템 개발
</Goal>

<Tech_Stack>
- Foundation: HTML5, Vanilla JavaScript (ES6+)
- Styling: Tailwind CSS (Play CDN), Lucide Icons (CDN)
- Communication: YouTube IFrame Player API, window.postMessage, Storage Event (localStorage)
- Deployment: 정적 파일 구조 (index.html 및 하위 html 파일들)
</Tech_Stack>

<Tasks>
### Step 1: 메인 호스트(Host) UI 및 엔진 구현
- **파일:** `index.html`
- **레이아웃:** 좌측 사이드바(메뉴) + 우측 메인 콘텐츠 영역(iframe 및 가이드 설명)
- **기능:** 메뉴 클릭 시 `iframe.src`를 변경하고 로딩 상태(Spinner) 표시. 
- **글로벌 알림:** iframe으로부터 오는 `postMessage`나 `storage` 변경 사항을 수신하여 호스트 UI 상단에 실시간 알림창(Toast) 띄우기.

### Step 2: 5가지 실무 사례 페이지 개발
1. **YouTube API Control (`yt.html`):**
   - iframe 내부에 유튜브 플레이어 삽입.
   - **핵심:** 부모 페이지에 [재생], [일시정지], [볼륨+] 버튼을 배치하고, YouTube Player API를 통해 iframe 내부 영상을 원격 제어.
2. **Security Sandbox (`sandbox.html`):**
   - 두 개의 iframe 배치. 하나는 `sandbox` 속성 없음, 하나는 `sandbox="allow-forms"`만 부여.
   - 내부 스크립트 실행 여부를 시각적으로 비교하여 보안 격리(Isolation)의 실효성 증명.
3. **Inline Payment Module (`widget.html`):**
   - **디자인:** Stripe/Toss 스타일의 신뢰감 있는 카드 입력 폼(마스킹 및 유효성 검사 포함).
   - **스타일 격리 증명:** 부모 페이지에서 강제로 버튼 스타일을 오염시켜도 iframe 내부는 영향을 받지 않음을 시각적으로 증명.
   - **양방향 통신:** 부모에서 쿠폰 적용 시 금액 업데이트(Parent->Child), 결제 완료 시 결과 보고(Child->Parent).
4. **MFE Data Sync (Option A - `mfe.html`):**
   - **시나리오:** '공유 투두리스트' 또는 '사용자 설정'.
   - **핵심:** iframe 내부에서 데이터를 수정하면 `localStorage`에 저장. 부모 페이지는 `storage` 이벤트를 감지하여 자신의 헤더나 상태바를 즉시 업데이트. (데이터 동기화 증명)
5. **Real-time Messaging (`postMessage.html`):**
   - 부모 입력창 -> iframe 출력 / iframe 버튼 클릭 -> 부모 알림.
   - 보안을 위해 `event.origin`을 검증하는 로직 반드시 포함.

### Step 3: 디자인 디테일링
- Tailwind CSS를 활용하여 다크 모드 스타일의 현대적인 대시보드 UI 적용.
- 각 예제 하단에 '이 기술의 핵심 포인트'와 '사용된 API'를 요약한 정보 카드 배치.

### 개선 사항
- **YouTube 컨트롤 고도화 (2026-03-10):**
    - 단순 볼륨 업 버튼을 호버 시 나타나는 슬라이더(Dropdown)로 변경.
    - 볼륨 아이콘 클릭 시 음소거(Mute/Unmute) 토글 기능 추가.
    - 재생/일시정지 버튼을 하나의 토글 버튼으로 통합하여 현재 상태에 따라 아이콘 자동 변경.
    - 5초 뒤로/앞으로 이동(`SEEK`) 기능 추가.
    - `postMessage`를 통한 양방향 제어 로직 유지 및 확장.

- **Sandbox 보안 모니터링 강화 (2026-03-12):**
    - **Security Auditor 패널 도입:** 부모 페이지(`sandbox.html`)에서 자식 iframe의 DOM 접근 가능 여부 및 `SecurityError`를 감지하여 실시간 보안 상태(Active/Blocked) 표시 기능 추가.
    - **중첩 iframe 보안 원리 명시:** 메인 호스트(`index.html`)의 `sandbox` 정책이 하위 iframe에 상속되는 'Double Lock' 구조를 시각적 가이드로 설명.
    - **디버깅 가이드 추가:** 스크립트 차단 시 내부 로그가 남지 않는 이유와 `allow-modals` 등 필수 정책 누락 시의 증상을 예제에 반영.

- **결제 위젯 보안 및 로직 고도화 (2026-03-17):**
    - **카드 번호 구역별 검열:** 보안성 향상을 위해 카드 번호 2구역과 4구역을 블랙 도트(•)로 실시간 마스킹 처리 (`0000-••••-0000-••••`).
    - **데이터 무결성 유지:** 마스킹된 출력값과 별개로 내부 `rawCardValue`를 통해 실제 결제 로직(16자리 유효성 검사) 수행.
</Tasks>

<Quality_and_Testing>
| 검증 항목 | 상세 내용 |
| :--- | :--- |
| **데이터 동기화** | MFE 예제에서 iframe의 변경사항이 부모 페이지에 새로고침 없이 반영되는가? |
| **보안 검증** | postMessage 수신 시 Origin 체크가 포함되어 있는가? |
| **API 연동** | YouTube IFrame API가 정상적으로 로드되고 원격 제어가 작동하는가? |
| **UI 반응성** | iframe 내부 콘텐츠의 높이에 따라 레이아웃이 유연하게 대응하는가? |
</Quality_and_Testing>

<Boundaries>
✅ **Always do:**
- 각 파일 최상단에 주석으로 파일명 명시 (예: ``)
- 모든 통신 로직에 `console.log`를 상세히 남겨 데이터 흐름 추적 가능하게 할 것
- `localStorage` 키값 충돌 방지를 위한 네임스페이스 사용

⚠️ **Ask first:**
- 외부 API 서버(Mock API) 연결이 필요한 추가 기능을 제안하고 싶을 때

🚫 **Never do:**
- Webpack/Vite 등 빌드 도구 사용 절대 금지 (순수 HTML/JS 파일로만 구성)
- `node_modules` 등 라이브러리 직접 설치 금지 (CDN만 사용)
</Boundaries>