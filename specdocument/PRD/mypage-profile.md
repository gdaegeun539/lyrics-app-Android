# 마이페이지/프로필 PRD

## 범위

마이페이지 홈, 사용자 프로필 조회, 내 노트, 북마크, 설정, 프로필 수정, 성별/출생연도 수정, 서비스 정보 링크를 정의한다.

## Android 화면명

- 기존: `MyPageScreen`, `SettingScreen`, `UserInfoScreen`, `EditProfileScreen`, `InternalWebViewScreen`, `NoteDetailScreen`
- 추가 구현 대상: `BlockedUsersScreen`, `DeleteAccountScreen`

## 요구사항

- 마이페이지는 사용자 프로필, 내 노트, 북마크 목록, 설정 진입을 제공한다.
- 사용자는 내 노트와 북마크한 노트를 확인할 수 있다.
- 내 노트 목록에서 좋아요, 북마크, 삭제, 상세 진입을 지원한다.
- 프로필 화면에서 닉네임/프로필 캐릭터 또는 성별/출생연도 정보를 수정할 수 있다.
- 설정 화면은 로그인 정보, 유저 ID, 약관/개인정보처리방침, FAQ, 문의, 버전 정보, 피드백, 로그아웃, 회원탈퇴, 차단 유저 목록 진입을 제공한다.
- 비로그인 사용자는 제한된 설정 정보와 로그인 유도 상태를 본다.

## 사용자 흐름

1. 하단 탭에서 `MyPageScreen`으로 이동한다.
2. 사용자 프로필과 내 노트/북마크 요약을 조회한다.
3. 내 노트 또는 북마크 탭에서 노트를 선택해 `NoteDetailScreen`으로 이동한다.
4. 설정 선택 시 `SettingScreen`으로 이동한다.
5. 사용자 정보 선택 시 `UserInfoScreen`으로 이동한다.
6. 프로필 수정 선택 시 `EditProfileScreen`으로 이동한다.
7. 약관/개인정보/FAQ/문의/피드백 링크는 `InternalWebViewScreen`으로 연다.

## 상태/이벤트

- 사용자 프로필 조회 실패 시 설정/프로필 영역에 오류 상태를 표시한다.
- 내 노트와 북마크 목록은 각각 pagination과 refresh 상태를 가진다.
- 프로필 수정은 변경된 필드만 요청한다.
- 성별/출생연도 수정은 변경 값이 있을 때 저장 버튼이 활성화된다.
- 로그아웃 성공 시 로컬 인증 데이터를 삭제하고 온보딩/로그인 그래프로 이동한다.

## API 요약

- `getUserProfile`: GET `/api/v1/users`
- `patchUserProfile`: PATCH `/api/v1/users`
  - 주요 body: 변경된 nickname/profile/gender/birthYear 필드
- `getMyNotes`: GET `/api/v1/notes`
  - 주요 query: `cursor`, `size`, `hasLyrics`, optional `artistId`
- `getMyNotesByBookmark`: GET `/api/v1/notes/bookmarked`
  - 주요 query: `cursor`, `size`, `hasLyrics`, optional `artistId`
- `getFavoriteArtistsHavingNotes`: GET `/api/v1/favorite-artists/having-notes`
- `getFavoriteArtistsBookmarked`: GET `/api/v1/favorite-artists/bookmarked`
- `getBlockedUsers`: GET `/api/v1/users/blocks`
- `deleteUser`: DELETE `/api/v1/auth/delete`

## 수용 기준

- 로그인 사용자가 마이페이지 진입 시 프로필 정보가 표시된다.
- 내 노트와 북마크 목록을 전환할 수 있다.
- 목록이 비어 있으면 빈 상태를 표시한다.
- 노트 선택 시 상세 화면으로 이동한다.
- 프로필 수정 성공 시 마이페이지 프로필 정보가 갱신된다.
- 설정의 외부/정책 링크는 웹뷰로 열린다.
- 비로그인 사용자는 로그인 정보 대신 로그인 유도 상태를 본다.
- 로그아웃 성공 시 인증 정보가 삭제되고 로그인 화면으로 이동한다.

## iOS Reference

- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/MyPageViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/UserProfileViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/EditUserInfoViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/ProfileEditViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/SettingViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/PageControl/MyNoteViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/PageControl/BookmarkViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/MyPageViewModel.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/MyNoteViewModel.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/BookmarkViewModel.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/EditUserInfoViewModel.swift`

## 기획 명세 출처

- 마이페이지 / 설정
- 마이페이지 / 내 노트
- 마이페이지 / 북마크
- 마이페이지 / 회원정보 수정
- 비로그인 유저용 마이페이지 / 설정

## Android Decision Needed

- 필수 확인: 비로그인 마이페이지에서 내 노트/북마크 영역을 숨길지, 로그인 필요 카드로 표시할지 결정해야 한다.
- 필수 확인: 프로필 수정과 성별/출생연도 수정을 한 화면으로 합칠지 iOS처럼 분리할지 결정해야 한다.
- 권장 확인: 다크모드 설정은 엑셀에 있으나 iOS 구현 기준과 Android 시스템 테마 정책을 맞춰야 한다.

## Open Questions

- FAQ, 문의, 피드백 URL은 코드/서버/운영 설정 중 어디에서 관리할지 확인이 필요하다.

