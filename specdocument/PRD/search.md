# 검색 PRD

## 범위

곡 검색 탭, 곡 선택 후 노트 검색 결과, 검색 결과 필터, 검색 결과에서 노트 상세 진입을 정의한다.

## Android 화면명

- 기존: `NoteSearchScreen`, `NoteSearchResultScreen`, `NoteDetailScreen`

## 요구사항

- 사용자는 곡명 또는 관련 검색어로 곡을 검색할 수 있다.
- 검색 결과에서 곡을 선택하면 해당 곡과 관련된 노트 목록을 볼 수 있다.
- 노트 검색 결과는 토픽/가사 포함 여부 등 구현된 필터를 제공한다.
- 검색 결과 노트에서도 좋아요, 북마크, 댓글/상세 진입, 신고/수정/삭제 동작을 제공한다.
- 비로그인 사용자는 검색과 상세 조회는 가능하되 로그인 필요한 액션은 제한한다.

## 사용자 흐름

1. 하단 탭에서 `NoteSearchScreen`으로 이동한다.
2. 검색어를 입력하면 곡 검색 결과가 표시된다.
3. 곡을 선택하면 `NoteSearchResultScreen`으로 이동한다.
4. 검색 결과에서 필터를 변경하면 목록이 갱신된다.
5. 노트를 선택하면 `NoteDetailScreen`으로 이동한다.

## 상태/이벤트

- 검색어가 없으면 기본/빈 상태를 표시한다.
- 검색어 변경 시 검색 결과 상태를 갱신한다.
- 곡 선택 후 결과 화면은 선택 곡 요약과 노트 목록을 함께 표시한다.
- 결과 목록은 pagination과 refresh 상태를 가진다.
- 검색 결과의 노트 액션은 홈/커뮤니티 노트 액션과 동일한 정책을 따른다.

## API 요약

- `getSearchedNotes`: GET `/api/v1/songs/search`
  - 주요 query: `query`, `pageNumber`, `pageSize`
- `getSongNotes`: GET `/api/v1/notes/songs`
  - 주요 query: `cursor`, `size`, `hasLyrics`, `songId`
- `getSongDetail`: GET `/api/v1/songs/{songID}`
- 공통 노트 액션: `postLikes`, `deleteLikes`, `postBookmarks`, `deleteBookmarks`, `deleteNote`, `reportNote`

## 수용 기준

- 검색어 입력 시 곡 검색 결과가 표시된다.
- 결과가 없으면 빈 상태를 표시한다.
- 곡 선택 시 선택 곡 기반 노트 결과 화면으로 이동한다.
- 결과 화면에서 선택 곡 요약 정보가 표시된다.
- 필터 변경 시 해당 조건으로 노트 목록을 다시 요청한다.
- 검색 결과 노트 선택 시 상세 화면으로 이동한다.
- 로그인 필요한 액션은 비로그인 상태에서 제한된다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/SearchNoteViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/SearchNoteViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/NoteDetailViewModel.swift`
- `app-iOS/Projects/Coordinator/SearchNote/Interface/Sources/SearchNoteCoordinatorInterface.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/GetSearchedNotesUseCase.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/GetSongNotesUseCase.swift`

## 기획 명세 출처

- 곡 검색(중앙탭) / 곡 검색 / 곡 검색
- 곡 검색(중앙탭) / 곡 검색 / 곡 선택
- 곡 검색(중앙탭) / 게시글 / 게시글 필터
- 비로그인 유저용 곡 검색(중앙탭) / 곡 검색

## Android Decision Needed

- 필수 확인: 검색어 입력 지연/debounce 기준을 Android에서 얼마로 둘지 결정해야 한다.
- 권장 확인: 검색 결과 Header가 스크롤에 따라 축소/확장되는 Android 기존 구현을 최종 UX로 유지할지 정한다.

## Open Questions

- iOS의 `/api/v1/songs/search`는 이름상 노트 검색과 곡 검색 역할이 혼재되어 보인다. Android 구현 시 서버 API 계약을 다시 확인해야 한다.

