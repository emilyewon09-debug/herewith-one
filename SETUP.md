# My Rhythm 웹사이트 설정 가이드

이 사이트는 정적 파일 하나(`index.html`)로 되어 있고, **GitHub Pages**에 올려서
본인의 구글(유튜브) 계정으로 로그인 → 내 재생목록 불러오기가 되도록 만들어졌어요.
아래 순서대로 딱 한 번만 설정하면 계속 쓸 수 있어요.

---

## 1단계. GitHub Pages에 사이트 올리기

1. [github.com](https://github.com) 계정이 없다면 만들어요 (무료).
2. 새 저장소(Repository)를 만들어요. 이름 예: `my-rhythm` (Public으로 설정)
3. 이 폴더 안의 `index.html` 파일을 그 저장소에 업로드해요.
   - GitHub 웹사이트에서 "Add file → Upload files"로 끌어다 놓기만 하면 돼요.
4. 저장소 설정(Settings) → 왼쪽 메뉴 **Pages** → Branch를 `main`, 폴더는 `/ (root)`로 선택 → Save
5. 몇 분 기다리면 다음과 같은 주소가 생겨요:
   ```
   https://내깃허브아이디.github.io/my-rhythm/
   ```
   이 주소가 **내 사이트 주소**예요. 3단계에서 이 주소가 필요해요.

---

## 2단계. Google Cloud에서 OAuth 클라이언트 만들기

1. [Google Cloud Console](https://console.cloud.google.com/)에 접속해서 새 프로젝트를 만들어요.
   (예: 프로젝트 이름 `my-rhythm`)
2. 왼쪽 메뉴 **API 및 서비스 → 라이브러리**로 이동 →
   "**YouTube Data API v3**"를 검색해서 **사용 설정(Enable)**해요.
3. 왼쪽 메뉴 **API 및 서비스 → OAuth 동의 화면**으로 이동해요.
   - User Type: **외부(External)** 선택
   - 앱 이름: `My Rhythm` (아무거나 괜찮아요)
   - 사용자 지원 이메일 / 개발자 연락처: 본인 이메일
   - 범위(Scopes) 단계는 건너뛰어도 돼요 (아래에서 자동으로 요청됨)
   - **테스트 사용자(Test users)** 단계에서 본인 구글 계정 이메일을 추가해요.
     (앱이 "게시(Publish)"되지 않은 상태에서는 등록된 테스트 사용자만 로그인 가능해요.
     본인만 쓸 거면 이 상태로 충분해요.)
4. 왼쪽 메뉴 **API 및 서비스 → 사용자 인증 정보(Credentials)** → **+ 사용자 인증 정보 만들기 → OAuth 클라이언트 ID**
   - 애플리케이션 유형: **웹 애플리케이션**
   - 이름: 아무거나 (예: `my-rhythm-web`)
   - **승인된 자바스크립트 원본(Authorized JavaScript origins)**에 1단계에서 확인한 주소를 추가해요:
     ```
     https://내깃허브아이디.github.io
     ```
     (경로 `/my-rhythm/` 없이, 도메인까지만 넣어요)
   - 만들기를 누르면 **클라이언트 ID**가 생성돼요. (`xxxxx.apps.googleusercontent.com` 형태)

---

## 3단계. 클라이언트 ID를 사이트에 붙여넣기

`index.html` 파일을 열어서 아래 부분을 찾아요:

```js
const CONFIG = {
  CLIENT_ID: "YOUR_GOOGLE_OAUTH_CLIENT_ID.apps.googleusercontent.com",
};
```

`YOUR_GOOGLE_OAUTH_CLIENT_ID.apps.googleusercontent.com` 부분을 2단계에서 발급받은
실제 클라이언트 ID로 바꾸고, 다시 GitHub 저장소에 업로드(덮어쓰기)해요.

---

## 4단계. 확인하기

1. `https://내깃허브아이디.github.io/my-rhythm/` 접속
2. "구글 계정으로 로그인" 클릭 → 본인 구글 계정 선택 → 동의
3. 사이드바에 내 유튜브 재생목록 목록이 뜨면 성공이에요.
   - 재생목록을 클릭해 펼치면 그 안의 곡들이 보이고, 곡을 클릭하면 선택된 날짜에 기록돼요.
   - "Focus용" 버튼을 누르면 그 재생목록이 Focus Mode에서 자동재생돼요.

---

## 참고 / 한계

- **유튜브 뮤직 전용 API는 따로 없어요.** 유튜브 뮤직에서 만든 재생목록도 결국 같은 유튜브 계정에
  저장되기 때문에, YouTube Data API v3로 대부분 그대로 불러와져요.
  다만 "좋아요 표시한 음악" 같은 유튜브 뮤직 특수 자동 재생목록은 API에서 접근이 제한될 수 있어요.
- 로그인 상태는 브라우저를 새로고침하면 풀려요 (다시 로그인하면 됨). 계속 로그인 상태를 유지하려면
  refresh token을 다루는 서버가 필요한데, 정적 사이트만으로는 구현이 어려워요. 지금 방식(매번 로그인)이
  개인용으로는 가장 간단하고 안전해요.
- OAuth 동의 화면을 "테스트" 상태로 두면 본인이 등록한 테스트 계정으로만 로그인돼요.
  본인만 쓸 사이트라면 이 상태 그대로 두는 게 제일 간단하고, 굳이 "게시(Publish)" 안 해도 돼요.
- 캘린더의 할 일 목록/D-Day는 아직 새로고침하면 초기화되는 임시 데이터예요.
  계속 저장되게 하려면 별도 데이터 저장 기능(예: Firebase, Supabase 같은 무료 백엔드)을 추가로 붙이면 돼요.
  원하면 그 부분도 다음 단계로 만들어줄 수 있어요.
