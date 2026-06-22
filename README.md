# Round & Go Frontend

제주 골프·여행 서비스 **ROUND & GO**의 React 프론트엔드입니다.  
골프장 검색, AI 코스 추천, 숙소·맛집·관광, 커뮤니티, 일정·날씨, 마이페이지, 이메일·카카오 로그인을 제공합니다.

## 기술 스택

| 항목 | 버전/도구 |
|------|-----------|
| React | 18.2 |
| React Router | 7.7 |
| styled-components | 6.1 |
| axios | 1.12 |
| react-datepicker, react-slick, swiper | 일정·캐러셀 UI |
| 빌드 | Create React App (`react-scripts` 5.0) |

## 시작하기

```bash
npm install
npm start
```

[http://localhost:3000](http://localhost:3000)에서 확인합니다.

| 명령어 | 설명 |
|--------|------|
| `npm start` | 개발 서버 |
| `npm run build` | 프로덕션 빌드 (`build/`) |
| `npm test` | 테스트 |

## 환경 변수

프로젝트 루트에 `.env` 파일을 생성합니다.

```env
REACT_APP_OPENWEATHER_API_KEY=your_openweather_api_key
REACT_APP_KAKAO_MAP_API_KEY=your_kakao_javascript_key
REACT_APP_API_SERVICE_KEY=your_tourapi_service_key
```

| 변수 | 용도 | 설정 파일 |
|------|------|-----------|
| `REACT_APP_OPENWEATHER_API_KEY` | OpenWeatherMap 날씨 API | `src/config/weather.js` |
| `REACT_APP_KAKAO_MAP_API_KEY` | 카카오맵 SDK | `src/config/kakaoConfig.js` |
| `REACT_APP_API_SERVICE_KEY` | 한국관광공사 TourAPI (숙소 등) | `src/Common/Accommodation/AccommodationAPI.js` |

환경 변수가 없으면 `weather.js`, `kakaoConfig.js`에 정의된 기본값이 사용됩니다. **프로덕션에서는 반드시 `.env`로 교체하세요.**

### OpenWeatherMap

1. [OpenWeatherMap](https://openweathermap.org/) 가입 → [API Keys](https://home.openweathermap.org/api_keys)에서 키 발급
2. `.env`에 `REACT_APP_OPENWEATHER_API_KEY` 설정
3. 새 키 활성화까지 **2~4시간** 소요 가능
4. 무료 플랜: 일 1,000회, 분당 60회

**테스트 URL:**
```
https://api.openweathermap.org/data/2.5/weather?q=제주시,KR&appid=YOUR_API_KEY&units=metric&lang=kr
```

**사용 API:** `weather` (현재), `forecast` (5일 예보) — `src/Schedule&Weather/services/weatherAPI.js`

### 카카오맵

1. [카카오 개발자 콘솔](https://developers.kakao.com/console/app) → 제품 설정 → **카카오맵** Web 활성화
2. JavaScript 키를 `REACT_APP_KAKAO_MAP_API_KEY`에 설정
3. SDK는 `src/Login/utils/kakaoMapLoader.js`에서 동적 로드 (`src/config/kakaoConfig.js`)

### TourAPI (한국관광공사)

1. [공공데이터포털](https://www.data.go.kr) → 「한국관광공사_관광정보서비스」 API 키 발급
2. `REACT_APP_API_SERVICE_KEY` 설정
3. 숙소 검색 등 `AccommodationAPI.js`에서 사용

### 카카오 로그인 (OAuth2)

- **현재 상태:** 홈 화면 카카오 버튼은 **점검 중** 안내(alert)만 표시됩니다. (`src/Login/HomePage.jsx`)
- 정상 연동 시: Spring Security OAuth2 → `https://api.roundandgo.com/oauth2/authorization/kakao`
- 콜백 라우트: `/login/oauth2/code/kakao` (`OAuth2Callback.jsx`)
- 인증 정보: **쿠키 기반** (`src/Login/utils/cookieUtils.js`) — JWT, refreshToken, user
- 관련 코드: `src/Login/Auth/oauth2KakaoConfig.js`, `sessionChecker.js`, `sessionSync.js`

**카카오 개발자 콘솔 설정 (복구 시):**
- Web 플랫폼: `http://localhost:3000`, 배포 도메인 등록
- Redirect URI: 백엔드 OAuth2 콜백 URL과 일치해야 함
- 동의항목: 프로필(닉네임/사진), 이메일(선택)

---

## 백엔드 API

기본 URL: `https://api.roundandgo.com` (`src/config/api.js`)

| 구분 | 엔드포인트 | 설명 |
|------|-----------|------|
| 인증 | `POST /api/auth/login` | 이메일 로그인 |
| 인증 | `POST /api/auth/signup` | 회원가입 |
| 인증 | `GET /api/user/me` | 사용자 정보 |
| 인증 | `GET /api/auth/token` | 토큰 |
| 계정 | `POST /api/auth/find-id/request` | 아이디 찾기 요청 |
| 계정 | `POST /api/auth/find-id/confirm` | 아이디 찾기 확인 |
| 골프장 | `GET /api/golf-courses/search` | 골프장 검색 |
| 관광 | `GET /api/tour-infos/{type}` | 맛집·관광 (지역/골프장 기반) |
| 일정 | `GET/POST /api/schedules` | 일정 CRUD |
| 일정 | `GET/PUT/DELETE /api/schedules/{id}` | 일정 상세 |
| 코스 | `GET /api/courses/saved` | 저장된 코스 |
| OAuth2 | `GET /oauth2/authorization/kakao` | 카카오 로그인 시작 |

커뮤니티 API는 `src/Common/Community/CommunityAPI.js`에서 `API_BASE_URL` 기준으로 호출합니다.

---

## 라우트

| 경로 | 페이지 | 설명 |
|------|--------|------|
| `/` | `Login/HomePage` | 로그인 홈 (이메일·카카오·회원가입) |
| `/email-login` | `EmailAuth/EmailLoginPage` | 이메일 로그인 |
| `/signup` | `EmailAuth/SignupPage` | 회원가입 |
| `/find-account` | `EmailAuth/FindAccountPage` | 아이디·비밀번호 찾기 |
| `/login/oauth2/code/kakao` | `OAuth2Callback` | 카카오 OAuth2 콜백 |
| `/first-main` | `FirstMain/FirstMainPage` | 제주 지도 + 골프장 선택·검색 |
| `/main` | `Main/Main/MainPage` | 메인 (숙소·맛집·관광·커뮤니티 미리보기) |
| `/detail/main` | `Main/Detail/DetailMain` | 상세 메인 |
| `/detail/main/more` | `Main/Detail/AccommodationD/MoreAccommodation` | 숙소 더보기 |
| `/community` | `Community/Community` | 커뮤니티 메인 |
| `/community/entire` | `Community/CommunityEntire` | 전체 게시글 |
| `/community/write` | `Community/CommunityWrite` | 글쓰기 |
| `/community/detail/:postId` | `Community/CommunityDetail` | 게시글 상세 |
| `/community/edit/:postId` | `Community/CommunityEdit` | 게시글 수정 |
| `/course/step1` ~ `/step3` | `Course/Components/CourseStep1~3` | 코스 추천 3단계 |
| `/course/my` | `Course/Components/MyCourseView` | 내 코스 |
| `/schedule` | `Schedule&Weather/SchedulePage` | 일정·캘린더·날씨 |
| `/jeju-location` | `Schedule&Weather/JejuLocationPage` | 제주 지역 선택 |
| `/mypage` | `MyPage/MyPage` | 마이페이지 |

**하단 Footer 탭** (`LayoutNBanner/Footer.jsx`): 홈 `/main`, 커뮤니티 `/community`, 코스추천 `/course`, 일정 `/schedule`, 마이페이지 `/mypage`

---

## 주요 기능

### 로그인·인증 (`Login/`)

- 이메일 로그인·회원가입·계정 찾기 (`authUtils.js`, `findAccountApi.js`)
- OAuth2 카카오 로그인 (현재 UI에서 일시 비활성)
- 쿠키 기반 세션, `cleanupLocalStorage.js`로 레거시 localStorage 정리
- 앱 레이아웃: 왼쪽 배너(`LeftContent`) + 오른쪽 스크롤 영역

### 첫 메인·골프장 (`FirstMain/`)

- 제주 13개 읍·면 지도 클릭 → 해당 지역 골프장 Top3
- 지역명 검색 (`FirstMain/Search.jsx`) → `GET /api/golf-courses/search`
- 토큰 확인: `IsContainToken.js`, `TokenCheking.js`

### 메인·상세 (`Main/`)

- **숙소:** TourAPI + 카테고리(프리미엄/가성비/감성) — `Common/Accommodation/`
- **맛집:** 백엔드 `tour-infos/restaurants` — `Common/Restaurant/`
- **관광:** TourAPI 관광지 목록
- **상세:** 숙소·맛집·관광 상세 (`Detail/AccommodationD`, `RestaurantD`, `TourismD`)

### 코스 추천 (`Course/`)

- 3단계 설문 (`CourseStep1` → `Step2` → `Step3`)
- `sessionStorage`에 단계별 데이터 저장
- 로그인 필요 (`getAuthToken` 검사)

### 커뮤니티 (`Community/`)

- 게시글 목록·상세·작성·수정·전체보기
- 인기글(`Common/Community/Popular`), 이미지 업로드, 카테고리 선택
- 검색: `GET /posts/search?keyword=`

### 일정·날씨 (`Schedule&Weather/`)

- 캘린더 UI, 일정 추가·수정 모달 (`AddScheduleModal`, `EditScheduleModal`)
- 백엔드 `ScheduleAPI.js` 연동 (Bearer 토큰)
- OpenWeatherMap 날씨 위젯, 제주 지역 선택
- 날씨 아이콘 SVG (`services/img/`)

### 마이페이지 (`MyPage/`)

- 프로필·설정 UI (`MyPageAPI.js`)

### 공통

- `ScreenSizeContext` — 반응형 화면
- `ScrollContext` — 메인 스크롤 ref
- `LoadingContext` + `LoadingPage` — 전역 로딩 오버레이

---

## 프로젝트 구조

```
RoundAndGo_Frontend/
├── public/
│   ├── index.html          # 메타·OG 태그, slick CSS
│   ├── greenlogo.svg
│   ├── _redirects          # SPA fallback
│   └── images/
│
└── src/
    ├── App.js              # 라우팅·레이아웃·Provider
    ├── config/             # api, kakao, weather 설정
    ├── assets/             # SVG·이미지 리소스
    │
    ├── Login/              # 홈, 이메일 인증, OAuth2, 쿠키 유틸
    ├── FirstMain/          # 지도·골프장 검색
    ├── Main/               # 메인·상세 (숙소/맛집/관광)
    ├── Course/             # 코스 추천 3단계
    ├── Community/          # 커뮤니티 CRUD
    ├── Schedule&Weather/   # 일정·날씨
    ├── MyPage/             # 마이페이지
    ├── LayoutNBanner/      # Header, Footer, LeftContent
    ├── Common/             # API·리스트·커뮤니티 공통
    └── Loading/            # 로딩 UI
```

---

## 배포

```bash
npm run build
```

`build/`를 정적 호스팅에 배포합니다. `public/_redirects`:

```
/*    /index.html   200
```

운영 API: `https://api.roundandgo.com`  
운영 사이트: `https://roundandgo.com`

---

## 문제 해결

| 증상 | 확인 사항 |
|------|-----------|
| `Invalid API key` (날씨) | OpenWeatherMap 키 활성화 대기, `.env` 설정 후 서버 재시작 |
| 날씨 미표시 | `REACT_APP_OPENWEATHER_API_KEY` 또는 `src/config/weather.js` |
| 카카오맵 로드 실패 | `REACT_APP_KAKAO_MAP_API_KEY`, 카카오맵 Web 플랫폼 활성화 |
| 카카오 로그인 | 현재 점검 중 — 이메일 로그인 사용 |
| 일정 API 401 | 쿠키 토큰 만료 — 재로그인 |
| TourAPI 오류 | `REACT_APP_API_SERVICE_KEY`, 공공데이터포털 키 상태 |
| CORS/네트워크 | `api.roundandgo.com` 서버 상태 확인 |

---

## 알려진 제한

- 카카오 로그인 버튼: 점검 안내만 동작 (`oauth2KakaoApi.startLogin()` 주석 처리)
- API 키 일부가 소스 기본값으로 포함 — 환경 변수로 교체 필요
- 클라이언트에 노출되는 API 키는 프로덕션에서 서버 프록시 권장

## 라이선스

Private (`"private": true`)
