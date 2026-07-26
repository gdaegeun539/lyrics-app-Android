# 알림 PRD

## 범위

개인 알림, 전체 알림, 알림 읽음 처리, 미확인 알림 표시를 정의한다. 엑셀의 알림 삭제/휴지통 기능은 전체 취소선 처리되어 본문 구현 범위에서 제외한다.

## Android 화면명

- 기존: `NotificationScreen`
- 관련 기존 진입점: `HomeScreen`, `MyPageScreen`

## 요구사항

- 사용자는 개인 알림과 전체 알림을 구분해서 볼 수 있다.
- 홈 또는 마이페이지에서 미확인 알림 여부를 표시할 수 있다.
- 알림 목록은 페이지네이션으로 조회한다.
- 알림을 선택하면 읽음 처리 후 연결된 노트 또는 관련 화면으로 이동한다.
- 알림 삭제/전체선택/휴지통 기능은 이번 본문 범위에 포함하지 않는다.

## 사용자 흐름

1. 홈 또는 마이페이지의 알림 아이콘을 선택한다.
2. `NotificationScreen`에서 개인 알림 탭과 전체 알림 탭을 본다.
3. 알림 목록을 스크롤하면 다음 페이지를 조회한다.
4. 알림을 선택하면 읽음 처리 API를 호출한다.
5. 알림 타입에 따라 노트 상세 또는 관련 화면으로 이동한다.

## 상태/이벤트

- `hasUncheckedNotification` 상태를 홈/마이페이지에서 표시한다.
- 개인 알림과 전체 알림은 별도 ViewModel 상태로 관리할 수 있다.
- 읽음 처리 성공 시 해당 알림 상태를 갱신한다.
- 알림 조회 실패 시 탭별 오류 상태를 표시한다.

## API 요약

- `getHasUncheckedNotification`: GET `/api/v1/notifications/check`
- `getPersonalNotifications`: GET `/api/v1/notifications/personal`
  - 주요 query: `cursor`, `size`
- `getPublicNotifications`: GET `/api/v1/notifications/public`
  - 주요 query: `cursor`, `size`
- `checkNotification`: PATCH `/api/v1/notifications/{notificationID}`

## 수용 기준

- 미확인 알림이 있으면 홈/마이페이지 알림 아이콘에 상태가 표시된다.
- 알림 화면에서 개인 알림과 전체 알림을 전환할 수 있다.
- 알림 목록은 추가 로딩을 지원한다.
- 알림 선택 시 읽음 처리 요청을 보낸다.
- 읽음 처리 성공 후 UI의 미확인 상태가 갱신된다.
- 삭제/휴지통 관련 UI는 노출하지 않는다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Notification/NoteNotificationContainerViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Notification/NotePersonalNotificationViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Notification/NotePublicNotificationViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/Notification/NotePersonalNotificationViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/Notification/NotePublicNotificationViewModel.swift`
- `app-iOS/Projects/Domain/Notification/Interface/Sources/UseCases/GetHasUncheckedNotificationUseCase.swift`
- `app-iOS/Projects/Domain/Notification/Interface/Sources/UseCases/CheckNotificationUseCase.swift`

## 기획 명세 출처

- 알림화면 / 내 소식 알림 / 내가 쓴 게시글 알림
- 알림화면 / 전체 알림 / 앱 전체 알림
- 알림화면 / 휴지통(알림 삭제) 항목은 취소선 제외

## Android Decision Needed

- 필수 확인: 알림 선택 후 이동할 상세 화면 매핑을 알림 타입별로 정의해야 한다.
- 권장 확인: 알림 탭 UI를 Android 하단/상단 탭 중 어떤 패턴으로 둘지 정한다.

## Open Questions

- 전체 알림이 노트 상세로 이동하는지, 공지 웹뷰/이벤트 화면으로 이동하는지 알림 타입별 정책 확인이 필요하다.
