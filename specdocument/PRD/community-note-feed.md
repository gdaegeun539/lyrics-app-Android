# 커뮤니티/노트 피드 PRD

## 범위

아티스트 커뮤니티 진입, 커뮤니티 기본 정보, 관심 아티스트 추가/제거, 커뮤니티 노트 피드, 필터, 좋아요, 북마크, 노트 메뉴 액션을 정의한다.

## Android 화면명

- 기존: `CommunityMainScreen`, `NoteDetailScreen`, `NoteFormScreen`, `NoteReportScreen`

## 요구사항

- 커뮤니티는 아티스트 이미지, 커뮤니티명, 관심 아티스트 상태를 표시한다.
- 사용자는 커뮤니티의 아티스트를 관심 아티스트에 추가하거나 제거할 수 있다.
- 커뮤니티 피드는 해당 아티스트 관련 노트를 페이지네이션으로 조회한다.
- 사용자는 가사 포함 노트만 필터링할 수 있다.
- 노트 항목은 작성자, 작성 시간, 본문, 가사, 곡 정보, 좋아요 수, 댓글 수, 북마크 상태를 표시한다.
- 사용자는 좋아요/좋아요 취소, 북마크/북마크 취소를 할 수 있다.
- 본인 노트에는 수정/삭제 메뉴를 제공한다.
- 타인 노트에는 신고/차단 메뉴를 제공한다.
- 비로그인 사용자는 커뮤니티와 노트 목록을 볼 수 있으나 로그인 필요한 액션은 제한한다.

## 사용자 흐름

1. 홈 또는 검색 결과에서 아티스트를 선택해 `CommunityMainScreen`으로 이동한다.
2. 화면 진입 시 아티스트 정보와 노트 목록을 조회한다.
3. 사용자가 가사 포함 필터를 변경하면 첫 페이지부터 재조회한다.
4. 노트를 선택하면 `NoteDetailScreen`으로 이동한다.
5. 글쓰기 버튼을 누르면 관심 아티스트 여부를 확인한 뒤 `NoteFormScreen`으로 이동한다.
6. 메뉴에서 수정 선택 시 `NoteFormScreen` edit mode로 이동한다.
7. 메뉴에서 삭제 선택 시 확인 후 노트를 삭제한다.
8. 메뉴에서 신고 선택 시 `NoteReportScreen`으로 이동한다.

## 상태/이벤트

- 최초 진입: 커뮤니티 기본 정보와 노트 첫 페이지를 조회한다.
- 필터 변경: `hasLyrics` 값을 바꿔 노트를 재조회한다.
- 추가 로딩: 마지막 cursor를 기준으로 다음 페이지를 조회한다.
- 좋아요/북마크: 낙관적 업데이트 후 실패 시 원복한다.
- 삭제 성공: 목록에서 제거하고 성공 상태를 표시한다.
- 차단 성공: 차단한 사용자의 콘텐츠를 숨기거나 목록을 재조회한다.

## API 요약

- `getArtist`: GET `/api/v1/artists/{artistID}`
- `getIsFavoriteArtist`: GET `/api/v1/favorite-artists/exists`
  - 주요 query: `artistId`
- `postFavoriteArtist` / `deleteFavoriteArtist`: POST/DELETE `/api/v1/favorite-artists`
  - 주요 query: `artistId`
- `getArtistNotes`: GET `/api/v1/notes/artists`
  - 주요 query: `cursor`, `size`, `hasLyrics`, `artistId`
- `postLikes` / `deleteLikes`: POST/DELETE `/api/v1/likes`
  - 주요 query: `noteId`
- `postBookmarks` / `deleteBookmarks`: POST/DELETE `/api/v1/bookmarks`
  - 주요 query: `noteId`
- `deleteNote`: DELETE `/api/v1/notes/{noteID}`
- `postBlockUserProfile`: POST `/api/v1/blocks`
  - 주요 query: `userId`

## 수용 기준

- 커뮤니티 진입 시 아티스트 정보와 노트 목록을 표시한다.
- 관심 아티스트 상태에 따라 하트 상태가 다르게 표시된다.
- 관심 아티스트 추가/제거 성공 시 상태가 즉시 반영된다.
- 가사 포함 필터를 켜면 가사가 있는 노트만 요청한다.
- 노트 목록은 다음 페이지가 있으면 추가 로딩된다.
- 본인 노트 메뉴에는 수정/삭제가 노출된다.
- 타인 노트 메뉴에는 신고/차단이 노출된다.
- 삭제 확인 후 성공하면 노트가 목록에서 제거된다.
- 비로그인 사용자가 좋아요/북마크/글쓰기/관심 추가를 시도하면 로그인 필요 상태로 처리한다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/CommunityMainViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/CommunityMainViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Note/NoteMenuViewConroller.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/Protocols/NoteMenuHandling.swift`
- `app-iOS/Projects/Coordinator/Home/Interface/Sources/HomeCoordinatorInterface.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/GetArtistNotesUseCase.swift`
- `app-iOS/Projects/Domain/Artist/Interface/Sources/UseCase/SetFavoriteArtistUseCase.swift`

## 기획 명세 출처

- 커뮤니티 피드 화면 / 커뮤니티 정보 / 커뮤니티 기본 정보
- 커뮤니티 피드 화면 / 커뮤니티 정보 / 관심 아티스트 추가/제거
- 커뮤니티 피드 화면 / 게시글 / 게시글 필터
- 커뮤니티 피드 화면 / 게시글 / 좋아요
- 커뮤니티 피드 화면 / 게시글 / 북마크
- 비로그인 유저용 커뮤니티 피드 화면 / 커뮤니티 정보

## Android Decision Needed

- 필수 확인: 비로그인 사용자의 커뮤니티 액션 제한 문구와 로그인 이동 목적지를 공통 정책으로 확정해야 한다.
- 필수 확인: 차단 성공 후 현재 목록에서만 제거할지 서버 재조회까지 수행할지 결정해야 한다.
- 권장 확인: 커뮤니티 글쓰기 버튼은 FloatingActionButton, 하단 버튼, Toolbar 액션 중 어떤 형태로 둘지 정한다.

## Open Questions

- 인기 정렬 공식은 엑셀에 있으나 iOS API 호출에서는 명시적인 sort 파라미터가 보이지 않는다. 서버 기본 정렬 정책 확인이 필요하다.
