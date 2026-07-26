# 공통 정책

## 요구사항

Android 구현은 iOS 현재 동작을 우선으로 한다. 엑셀 명세는 기능 의도를 보조하지만, 취소선 처리된 항목과 어드민 기능은 제외한다. iOS에 구현된 기능이 엑셀에 없거나 설명이 약한 경우에도 Android 구현 대상에 포함한다.

## 공통 사용자 상태

- 로그인 사용자: 인증 토큰을 보유하고 홈, 검색, 커뮤니티, 노트 작성, 댓글, 좋아요, 북마크, 신고, 차단, 마이페이지 기능을 사용할 수 있다.
- 비로그인 사용자: iOS 구현에서 허용된 탐색 범위까지만 제공한다. 로그인 필요한 액션은 로그인 유도 또는 차단 상태로 처리한다.
- 첫 방문 사용자: iOS `checkFirstVisitor` 및 홈의 초기 관심 아티스트 선택 흐름을 참고한다.

## 공통 화면/내비게이션

- Android 기존 화면명: `SplashScreen`, `LoginScreen`, `OnboardingTermsScreen`, `OnboardingGenderAgeScreen`, `ProfileScreen`, `WelcomeScreen`, `HomeScreen`, `CommunityMainScreen`, `NoteDetailScreen`, `NoteFormScreen`, `SearchSongScreen`, `NoteSearchScreen`, `NoteSearchResultScreen`, `MyPageScreen`, `SettingScreen`, `UserInfoScreen`, `InternalWebViewScreen`
- Android 추가 구현 대상 화면명: `NotificationScreen`, `ReportScreen`, `BlockedUsersScreen`, `DeleteAccountScreen`, `FavoriteArtistSelectScreen`
- iOS는 Coordinator 기반이고 Android는 Compose Navigation 기반이므로 화면 전환은 Android Navigation Graph에서 명시적으로 관리한다.

## 공통 상태/이벤트

- 목록형 데이터는 최초 로딩, 추가 로딩, 새로고침, 빈 상태, 실패 상태를 가진다.
- 좋아요와 북마크는 iOS ViewModel처럼 낙관적 업데이트 후 실패 시 이전 상태로 되돌린다.
- 삭제/차단/신고/회원탈퇴처럼 되돌리기 어려운 작업은 확인 다이얼로그를 둔다.
- 네트워크 오류는 서버 에러 메시지와 에러 코드를 표시할 수 있어야 한다.
- 앱 업데이트 필요, 인증 정보 없음, 중복 로그인 등 전역 오류는 iOS `Coordinator.handleError` 정책을 따른다.

## API 요약 기준

- API 정보는 `FeelinAPI` enum case와 method/path를 기준으로 요약한다.
- request/response DTO 전체는 문서에 복사하지 않는다.
- 실제 구현 시에는 Android 네트워크 계층 또는 서버 API 문서에서 endpoint 계약을 다시 조회한다.

## iOS Reference

- `app-iOS/Projects/Core/Network/Interface/Sources/API/FeelinAPI.swift`
- `app-iOS/Projects/Coordinator/App/Interface/Sources/Root/CoordinatorInterface.swift`
- `app-iOS/Projects/Coordinator/TabBar/Interface/Sources/TabBarCoordinatorInterface.swift`
- `app-iOS/Projects/App/Sources/Application/AppCoordinator.swift`

## Android Decision Needed

- 필수 확인: 비로그인 사용자의 로그인 유도 문구와 이동 위치를 Android 전체에서 통일해야 한다.
- 필수 확인: 전역 인증 오류 발생 시 토큰 삭제 후 온보딩으로 복귀하는 정책을 Android에서 동일하게 적용할지 확정해야 한다.
- 권장 확인: iOS의 UIKit alert/present 흐름을 Android Compose `Dialog`, `ModalBottomSheet`, `Snackbar` 중 어떤 컴포넌트로 치환할지 공통 UI 정책을 정한다.

## Open Questions

- 비로그인 탐색 범위는 iOS 구현 기준으로 제한하되, Android 기존 구현에 더 넓은 탐색이 이미 있는 경우 유지할지 축소할지 확인이 필요하다.
- 엑셀의 일부 Android 전용 UX 요구가 iOS 구현과 다를 때 제품 정책으로 채택할지 `future-or-excluded.md`로 보낼지 기능별 판단이 필요하다.

