# 이벤트/배너 PRD

## 범위

홈 배너, 이벤트 조회, 이벤트 거절 처리를 정의한다. 엑셀의 행사 정보 탭은 iOS에 이벤트 API가 있으나 Android 현재 하단 탭 구조와 다를 수 있어 본문에는 iOS 구현 범위의 이벤트/배너만 포함한다.

## Android 화면명

- 기존: `HomeScreen`, `InternalWebViewScreen`
- 추가 구현 대상: 필요 시 `EventDetailScreen`

## 요구사항

- 홈은 서버에서 내려온 배너 목록을 표시한다.
- 배너 선택 시 연결 URL 또는 연결 타입에 따라 웹뷰/외부 이동을 수행한다.
- 앱은 단일 이벤트 정보를 조회할 수 있다.
- 사용자는 이벤트 노출을 거절하거나 닫을 수 있다.
- 이벤트 거절 성공 시 동일 이벤트를 다시 노출하지 않는다.

## 사용자 흐름

1. `HomeScreen` 진입 시 배너 목록과 이벤트 정보를 조회한다.
2. 배너가 있으면 홈 상단 또는 지정 영역에 표시한다.
3. 배너 선택 시 링크를 연다.
4. 이벤트가 있으면 팝업/배너/모달 등 Android 결정 UI로 표시한다.
5. 사용자가 거절/닫기를 선택하면 이벤트 거절 API를 호출한다.

## 상태/이벤트

- 배너 목록은 홈 데이터와 함께 조회된다.
- 이벤트 정보는 홈 진입 또는 정책상 필요한 시점에 조회한다.
- 이벤트 거절 성공 시 로컬 상태의 이벤트를 제거한다.
- 배너/이벤트 조회 실패는 홈 전체 실패로 만들지 않고 해당 영역만 비운다.

## API 요약

- `getBanners`: GET `/api/v1/banners`
- `getEvents`: GET `/api/v1/events`
- `postEventRefuse`: POST `/api/v1/events/refuse`
  - 주요 query: `eventId`

## 수용 기준

- 홈 진입 시 배너가 있으면 표시된다.
- 배너 선택 시 연결 링크가 열린다.
- 배너 조회 실패가 홈 노트/아티스트 조회를 막지 않는다.
- 이벤트가 있으면 Android 결정 UI로 표시된다.
- 이벤트 거절 성공 시 현재 세션에서 이벤트가 사라진다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/HomeViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/Views/Cells+SupplementaryViews/BannerCell.swift`
- `app-iOS/Projects/Domain/Shared/Interface/Sources/UseCasess/FetchSingleEventUseCase.swift`
- `app-iOS/Projects/Domain/Shared/Interface/Sources/UseCasess/FetchBannersUseCase.swift`
- `app-iOS/Projects/Domain/Shared/Interface/Sources/UseCasess/RefuseEventUseCase.swift`

## 기획 명세 출처

- 홈화면(좌측탭) / 사용자 피드백 / 사용자 피드백
- 행사 정보(오른쪽에서 두번째 탭) 항목은 별도 탭 구현 근거가 부족하므로 추후/확인 대상으로 분리

## Android Decision Needed

- 필수 확인: 이벤트를 홈 팝업으로 표시할지, 별도 탭/상세 화면으로 구현할지 결정해야 한다.
- 필수 확인: 엑셀의 행사 정보 탭을 이번 Android 구현 범위에 포함할지 제외할지 확정해야 한다. iOS `getEvents`는 있으나 탭 구현 근거와 완전히 일치하지 않는다.
- 권장 확인: 배너 링크를 웹뷰로 열지 외부 브라우저로 열지 공통 링크 정책과 맞춘다.

## Open Questions

- 이벤트와 행사 정보가 같은 도메인인지, 홈 이벤트와 별도 공연/행사 탭인지 제품 정책 확인이 필요하다.

