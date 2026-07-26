# 온보딩/인증 PRD

## 범위

로그인, 약관 동의, 성별/출생연도 입력, 프로필 설정, 가입 완료 웰컴까지의 신규 사용자 흐름을 정의한다.

## Android 화면명

- 기존: `LoginScreen`, `OnboardingTermsScreen`, `OnboardingGenderAgeScreen`, `ProfileScreen`, `WelcomeScreen`, `InternalWebViewScreen`
- 보류: `OnboardingFavoriteArtistScreen`은 Android 코드에 있으나 현재 활성 온보딩 라우트에 연결되지 않은 parked subfeature다.

## 요구사항

- 사용자는 Apple 또는 Kakao 소셜 로그인으로 인증을 시작한다.
- 최근 로그인 수단이 있으면 로그인 화면에서 식별 가능한 상태로 보여준다.
- 로그인 성공 후 신규 회원이면 약관 동의로 이동한다.
- 필수 약관은 모두 동의해야 다음 단계로 이동할 수 있다.
- 약관 보기 선택 시 내부 웹뷰로 약관 URL을 연다.
- 성별/출생연도 입력은 건너뛸 수 있으며, 건너뛰면 해당 값은 가입 요청에 포함하지 않는다.
- 프로필 단계에서 닉네임과 프로필 캐릭터를 선택하고 회원가입을 완료한다.
- 가입 성공 후 웰컴 화면을 거쳐 메인 화면으로 진입한다.

## 사용자 흐름

1. `LoginScreen`에서 소셜 로그인 버튼을 선택한다.
2. OAuth 결과가 기존 사용자면 메인 그래프로 이동한다.
3. OAuth 결과가 신규 사용자면 `OnboardingTermsScreen`으로 이동한다.
4. 필수 약관 전체 동의 후 `OnboardingGenderAgeScreen`으로 이동한다.
5. 성별/출생연도 입력 또는 건너뛰기 후 `ProfileScreen`으로 이동한다.
6. 닉네임/프로필 캐릭터 입력 후 가입을 요청한다.
7. 가입 성공 시 `WelcomeScreen`으로 이동하고, 완료 버튼으로 메인 그래프에 진입한다.

## 상태/이벤트

- `OnboardingViewModel`은 약관, 성별, 출생연도, 닉네임, 프로필 캐릭터를 parent graph scoped state로 유지한다.
- 가입 중 중복 요청은 무시한다.
- 필수 약관 미동의 시 가입 요청을 막고 오류 상태를 표시한다.
- OAuth access token 또는 provider가 없으면 가입 요청을 막는다.
- 가입 성공 후 로컬 사용자 프로필과 온보딩 완료 상태를 저장한다.

## API 요약

- `login`: POST `/api/v1/auth/sign-in`
  - 주요 body: `socialAccessToken`, `authProvider`
  - 성공 시 기존 사용자 로그인 또는 신규 사용자 가입 필요 상태를 반환한다.
- `signUp`: POST `/api/v1/auth/sign-up`
  - 주요 body: 소셜 토큰, provider, nickname, profile character, optional gender/birthYear, terms
  - 성공 시 인증 토큰과 사용자 가입 완료 상태를 저장한다.
- `checkUserValidity`: GET `/api/v1/auth/validate-token`
  - 자동 로그인 또는 토큰 유효성 확인에 사용한다.
- `reissueAccessToken`: POST `/api/v1/auth/token`
  - refresh token으로 access token을 재발급한다.

## 수용 기준

- 로그인 버튼 선택 시 해당 OAuth provider 플로우가 시작된다.
- OAuth 취소 시 별도 오류 없이 로그인 화면에 머문다.
- 필수 약관 전체 동의 전에는 다음 단계로 이동할 수 없다.
- 약관 보기 선택 시 `InternalWebViewScreen`으로 이동한다.
- 성별/출생연도 건너뛰기 시 가입 요청에서 두 값이 빠진다.
- 닉네임이 비어 있으면 가입 완료 버튼이 활성화되지 않는다.
- 가입 성공 시 로컬 사용자 데이터가 저장되고 웰컴 화면으로 이동한다.
- 가입 실패 시 서버 에러 메시지와 에러 코드를 표시한다.

## iOS Reference

- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewControllers/LoginViewController.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewControllers/UseAgreementViewController.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewControllers/UserInformationViewController.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewControllers/ProfileViewController.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewControllers/WelcomeViewController.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewModel/LoginViewModel.swift`
- `app-iOS/Projects/Feature/Onboarding/Interface/Sources/ViewModel/ProfileViewModel.swift`
- `app-iOS/Projects/Coordinator/Onboarding/Interface/Sources/OnboardingCoordinatorInterface.swift`

## 기획 명세 출처

- 온보딩 / 회원가입 / 로그인
- 온보딩 / 회원가입 / 약관 동의
- 온보딩 / 회원가입 / 회원정보 수집
- 온보딩 / 회원가입 / 프로필 설정
- 온보딩 / 회원가입 / 회원가입 건너뛰기

## Android Decision Needed

- 필수 확인: `회원가입 건너뛰기`는 엑셀에 있으나 iOS 현재 온보딩 흐름과 Android 활성 라우트에서 명확한 구현 근거가 약하다. Android에서 비로그인 홈 진입을 제공할지 결정해야 한다.
- 필수 확인: 관심 아티스트 선택은 iOS 홈 첫 방문 흐름에 있고 Android `favoriteartist`는 parked subfeature다. 온보딩에 넣을지 홈 첫 진입으로 둘지 결정해야 한다.
- 권장 확인: 닉네임 byte 제한과 특수문자 금지 정책은 서버 검증과 Android 입력 필터 중 어디까지 선제 적용할지 정한다.

## Open Questions

- 최근 로그인 표시의 만료 기준은 엑셀에는 2주로 쓰여 있으나 iOS 저장 정책과 Android 저장 정책을 다시 확인해야 한다.

