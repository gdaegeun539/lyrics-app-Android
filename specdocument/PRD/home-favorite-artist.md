# 홈/관심 아티스트 PRD

## 범위

홈 화면, 배너, 관심 아티스트 목록, 관심 아티스트 선택/검색, 관심 아티스트 기반 최신 노트 피드를 정의한다.

## Android 화면명

- 기존: `HomeScreen`, `CommunityMainScreen`, `InternalWebViewScreen`
- 추가 구현 대상: `FavoriteArtistSelectScreen`

## 요구사항

- 홈은 배너, 관심 아티스트, 관심 아티스트 관련 최신 노트 피드를 보여준다.
- 첫 방문 사용자는 관심 아티스트 선택 화면을 1회 볼 수 있다.
- 관심 아티스트가 없거나 비로그인 상태일 때도 홈 화면은 안정적으로 표시되어야 한다.
- 사용자는 아티스트 검색을 통해 관심 아티스트를 추가할 수 있다.
- 관심 아티스트 선택은 최대 30개 제한을 둔다.
- 관심 아티스트 또는 찾아보기 아티스트를 선택하면 해당 아티스트의 커뮤니티로 이동한다.
- 홈의 노트 피드는 좋아요, 북마크, 댓글 진입, 노트 상세 진입, 신고/차단/삭제/수정 진입을 제공한다.

## 사용자 흐름

1. 메인 진입 시 `HomeScreen`에서 배너, 관심 아티스트, 관련 노트를 조회한다.
2. 첫 방문이면 `FavoriteArtistSelectScreen`을 표시한다.
3. 사용자가 아티스트를 검색하고 선택한 뒤 완료하면 관심 아티스트 목록을 저장한다.
4. 홈에서 관심 아티스트를 누르면 `CommunityMainScreen`으로 이동한다.
5. 노트 영역을 누르면 `NoteDetailScreen`으로 이동한다.
6. 배너 또는 외부 링크를 누르면 `InternalWebViewScreen` 또는 외부 브라우저 정책에 따라 연다.

## 상태/이벤트

- 홈 데이터는 배너, 관심 아티스트, 관심 아티스트 관련 노트를 병렬 조회한다.
- 홈 피드는 pull-to-refresh를 지원한다.
- 좋아요/북마크는 낙관적 업데이트를 적용하고 실패 시 원복한다.
- 노트 삭제 성공 시 목록에서 해당 노트를 제거한다.
- 차단 성공 시 차단 대상 사용자의 노트를 목록에서 제거하거나 재조회한다.

## API 요약

- `getBanners`: GET `/api/v1/banners`
- `getFavoriteArtists`: GET `/api/v1/favorite-artists`
  - 주요 query: `cursor`, `size`
- `getFavoriteArtistsRelatedNotes`: GET `/api/v1/notes`
  - 주요 query: `cursor`, `size`, `hasLyrics`, `isFavoriteArtistsOnly=true`
- `getArtists`: GET `/api/v1/artists`
  - 주요 query: `pageNumber`, `pageSize`
- `searchArtists`: GET `/api/v1/artists/search`
  - 주요 query: `query`, `pageNumber`, `pageSize`
- `postFavoriteArtists`: POST `/api/v1/favorite-artists/batch`
  - 주요 body: `artistIds`
- `postFavoriteArtist` / `deleteFavoriteArtist`: POST/DELETE `/api/v1/favorite-artists`
- `checkFirstVisitor`: GET `/api/v1/users/first-time`

## 수용 기준

- 홈 진입 시 배너, 관심 아티스트, 노트 피드가 표시된다.
- 데이터가 없으면 빈 상태가 깨지지 않고 표시된다.
- 관심 아티스트 선택에서 1개 이상 선택해야 완료할 수 있다.
- 31번째 아티스트 선택은 막고 최대 30개 제한 메시지를 표시한다.
- 검색어 변경 시 아티스트 검색 결과가 갱신된다.
- 관심 아티스트 선택 완료 후 홈 데이터가 갱신된다.
- 노트 좋아요/북마크 실패 시 UI 상태가 이전 값으로 복구된다.
- 배너 링크는 안전한 웹뷰/외부 링크 정책으로 열린다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/HomeViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/ArtistSelectViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/SearchMoreFavoriteArtistViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/HomeViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/ArtistSelectViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/SearchMoreFavoriteArtistViewModel.swift`
- `app-iOS/Projects/Coordinator/Home/Interface/Sources/HomeCoordinatorInterface.swift`

## 기획 명세 출처

- 홈화면(좌측탭) / 사용자 맞춤 선택 / 아티스트 검색
- 홈화면(좌측탭) / 사용자 맞춤 선택 / 아티스트 선택
- 홈화면(좌측탭) / 관심 아티스트 / 아티스트 레코드 진입
- 홈화면(좌측탭) / 관심 커뮤니티의 최신 게시글 / 최신 게시글 피드
- 비로그인 유저용 홈화면(좌측탭) / 아티스트 찾아보기

## Android Decision Needed

- 필수 확인: Android의 `OnboardingFavoriteArtistScreen`을 홈 첫 방문 선택 화면으로 재사용할지, 별도 `FavoriteArtistSelectScreen`으로 만들지 결정해야 한다.
- 필수 확인: 비로그인 홈에서 관심 아티스트 영역을 비워둘지, 추천/전체 아티스트를 노출할지 결정해야 한다.
- 권장 확인: 배너 클릭을 내부 웹뷰로 통일할지 외부 브라우저/딥링크를 허용할지 정한다.

## Open Questions

- iOS의 first visitor API가 관심 아티스트 선택 팝업/화면 노출 여부만 의미하는지, 배너/이벤트 노출 정책과 연결되는지 확인이 필요하다.

