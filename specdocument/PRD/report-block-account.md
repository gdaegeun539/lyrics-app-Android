# 신고/차단/계정 PRD

## 범위

노트/댓글 신고, 사용자 차단/차단 해제, 차단 사용자 목록, 로그아웃, 회원탈퇴를 정의한다.

## Android 화면명

- 추가 구현 대상: `ReportScreen`, `BlockedUsersScreen`, `DeleteAccountScreen`
- 관련 기존 화면: `SettingScreen`, `NoteDetailScreen`, `CommunityMainScreen`, `MyPageScreen`

## 요구사항

- 사용자는 타인의 노트 또는 댓글을 신고할 수 있다.
- 신고 화면은 신고 대상과 신고 사유를 구분해야 한다.
- 신고 성공 후 이전 화면으로 돌아가거나 완료 상태를 표시한다.
- 사용자는 타인을 차단할 수 있다.
- 설정에서 차단한 사용자 목록을 확인하고 차단 해제할 수 있다.
- 사용자는 로그아웃할 수 있다.
- 사용자는 회원탈퇴를 요청할 수 있다.

## 사용자 흐름

1. 노트 또는 댓글 메뉴에서 신고를 선택한다.
2. `ReportScreen`에서 신고 사유를 선택하고 제출한다.
3. 제출 성공 시 완료 상태를 표시하고 이전 화면으로 돌아간다.
4. 노트/댓글 메뉴에서 사용자 차단을 선택하면 확인 후 차단 요청을 보낸다.
5. 설정에서 `BlockedUsersScreen`으로 이동해 차단 목록을 확인한다.
6. 차단 해제 선택 시 해당 사용자를 목록에서 제거한다.
7. 설정에서 로그아웃 또는 회원탈퇴를 선택하면 확인 후 처리한다.

## 상태/이벤트

- 신고는 note id 또는 comment id 중 하나를 대상으로 한다.
- 신고 사유 미선택 시 제출할 수 없다.
- 중복 신고 등 서버 오류는 오류 메시지를 표시한다.
- 차단 성공 시 관련 콘텐츠를 숨기거나 재조회한다.
- 차단 해제 성공 시 목록에서 제거한다.
- 회원탈퇴 성공 시 로컬 인증/사용자 데이터를 삭제한다.

## API 요약

- `reportNote`: POST `/api/v1/reports`
  - 주요 body: target id, target type, reason
- `postBlockUserProfile`: POST `/api/v1/blocks`
  - 주요 query: `userId`
- `deleteBlockUserProfile`: DELETE `/api/v1/blocks`
  - 주요 query: `userId`
- `getBlockedUsers`: GET `/api/v1/users/blocks`
- `deleteUser`: DELETE `/api/v1/auth/delete`
- 로그아웃은 iOS `LogoutUseCase`처럼 로컬 인증 데이터 삭제 중심으로 처리한다.

## 수용 기준

- 타인 노트/댓글 메뉴에서 신고 화면으로 이동할 수 있다.
- 신고 사유를 선택하지 않으면 제출할 수 없다.
- 신고 성공 시 완료 상태를 표시한다.
- 차단 확인 후 성공하면 대상 사용자의 콘텐츠가 현재 목록에서 사라진다.
- 차단 목록에서 차단 해제를 할 수 있다.
- 로그아웃 성공 시 로컬 인증 정보가 삭제되고 로그인 화면으로 이동한다.
- 회원탈퇴 성공 시 로컬 인증 정보가 삭제되고 로그인 화면으로 이동한다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/ReportViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/ReportViewModel.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/BlockedUsersViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewControllers/DeleteUserViewController.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/BlockedUsersViewModel.swift`
- `app-iOS/Projects/Feature/MyPage/Interface/Sources/ViewModels/DeleteUserViewModel.swift`
- `app-iOS/Projects/Domain/Report/Interface/Sources/UseCases/ReportNoteUseCase.swift`
- `app-iOS/Projects/Domain/UserProfile/Interface/Sources/UseCase/BlockUserUseCase.swift`
- `app-iOS/Projects/Domain/UserProfile/Interface/Sources/UseCase/FetchBlockedUsersUseCase.swift`
- `app-iOS/Projects/Domain/UserProfile/Interface/Sources/UseCase/DeleteUserUseCase.swift`

## 기획 명세 출처

- 홈화면(좌측탭) / 관심 커뮤니티의 최신 게시글 / 신고하기
- 커뮤니티 피드 화면 / 게시글 / 신고하기
- 마이페이지 / 설정 / 차단한 유저
- 마이페이지 / 설정 / 로그아웃
- 마이페이지 / 설정 / 회원탈퇴

## Android Decision Needed

- 필수 확인: 신고 대상이 댓글일 때도 iOS `reportNote` API abstraction을 그대로 따를지 Android API layer에서 target type을 명확히 분리할지 결정해야 한다.
- 필수 확인: 차단 후 현재 화면에서 즉시 숨길 콘텐츠 범위를 노트만으로 할지 댓글까지 포함할지 결정해야 한다.
- 권장 확인: 회원탈퇴 사유 수집이 필요한지 확인한다.

## Open Questions

- 신고 사유 enum과 서버 에러 코드는 서버 계약 확인이 필요하다.

