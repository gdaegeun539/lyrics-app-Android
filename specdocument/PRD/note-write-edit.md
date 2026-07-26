# 노트 작성/수정 PRD

## 범위

노트 작성, 노트 수정, 곡 추가/검색, 가사 입력, 가사 배경 선택, 본문 작성, 카테고리 선택을 정의한다.

## Android 화면명

- 기존: `NoteFormScreen`, `SearchSongScreen`

## 요구사항

- 사용자는 커뮤니티에서 노트 작성 화면으로 진입한다.
- 작성 모드는 신규 작성과 수정 모드를 구분한다.
- 사용자는 카테고리를 선택할 수 있다.
- 해석공유 카테고리는 곡/가사/본문 입력을 기본 구조로 한다.
- 자유/질문 카테고리는 본문만 작성할 수 있고, 필요 시 곡/가사 섹션을 추가할 수 있다.
- 곡 검색 화면에서 아티스트 기준 곡을 검색하고 선택할 수 있다.
- 가사를 직접 입력하거나 검색/선택할 수 있다.
- 가사 배경을 선택할 수 있다.
- 수정 모드에서는 기존 노트 데이터를 표시하고, iOS 기준으로 추가된 곡은 수정할 수 없다.
- 임시저장은 iOS 현재 구현 근거가 약하므로 본문 구현 범위에서 제외한다.

## 사용자 흐름

1. `CommunityMainScreen`에서 글쓰기 버튼을 누른다.
2. 관심 아티스트 조건을 만족하면 `NoteFormScreen` create mode로 이동한다.
3. 카테고리를 선택한다.
4. 곡 추가를 선택하면 `SearchSongScreen`으로 이동한다.
5. 곡 선택 후 작성 화면으로 돌아와 곡 정보를 표시한다.
6. 가사 입력/검색, 배경 선택, 본문 입력을 완료한다.
7. 등록 버튼으로 노트 생성을 요청한다.
8. 기존 노트 수정 선택 시 `NoteFormScreen` edit mode로 이동해 가사/본문을 수정한다.

## 상태/이벤트

- 작성 화면은 카테고리, 선택 곡, 가사, 가사 배경, 본문, sheet/dialog 상태를 가진다.
- 곡 삭제 요청 시 확인 다이얼로그를 표시한다.
- 곡 없이 작성 가능한 카테고리에서 필요한 경우 곡 없음 경고를 표시한다.
- 등록/수정 요청 중 중복 제출을 막는다.
- 생성/수정 성공 시 이전 화면으로 돌아가 목록을 갱신한다.

## API 요약

- `searchSongs`: GET `/api/v1/songs/search/artists`
  - 주요 query: `query`, `cursor`, `size`, `artistId`
- `postNote`: POST `/api/v1/notes`
  - 주요 body: artist/song, topic, lyrics, lyrics background, body
- `patchNote`: PATCH `/api/v1/notes/{noteID}`
  - 주요 body: 수정 가능한 가사/본문/배경/카테고리 값
- `getSongDetail`: GET `/api/v1/songs/{songID}`
  - 선택 곡 상세 표시가 필요한 경우 사용한다.

## 수용 기준

- 신규 작성 진입 시 빈 작성 상태가 표시된다.
- 수정 진입 시 기존 노트 데이터가 표시된다.
- 카테고리 선택 상태가 화면에 반영된다.
- 곡 검색어 입력 시 검색 결과가 표시된다.
- 곡 선택 후 작성 화면에 곡 정보가 반영된다.
- 곡 삭제 확인 후 곡/가사 섹션 상태가 일관되게 갱신된다.
- 가사 배경 선택 후 작성 화면에 반영된다.
- 필수 입력이 부족하면 등록/수정 요청을 보낼 수 없다.
- 등록/수정 성공 시 이전 피드 또는 상세 화면이 갱신된다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/WritingNote/PostNoteViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/WritingNote/EditNoteViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/WritingNote/SearchSongViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/WritingNote/LyricsBackgroundViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/PostNoteViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/EditNoteViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/SearchSongViewModel.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/PostNoteUseCase.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/PatchNoteUseCase.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/SearchSongUseCase.swift`

## 기획 명세 출처

- 게시글 작성 화면 / 곡 정보 추가 / 곡 추가
- 게시글 작성 화면 / 곡 정보 추가 / 곡 추가 - 검색창
- 게시글 작성 화면 / 가사 정보 추가 / 가사 입력
- 게시글 작성 화면 / 가사 정보 추가 / 가사 배경 선택
- 게시글 작성 화면 / 감상 내용 작성 / 게시글 내용 작성
- 커뮤니티 피드 화면 / 글쓰기 페이지 진입 / 게시글 작성

## Android Decision Needed

- 필수 확인: 수정 모드에서 곡 변경을 금지할지, Android에서는 허용할지 결정해야 한다. iOS/엑셀 기준은 곡 수정 불가다.
- 필수 확인: 자유/질문 카테고리에서 곡 없는 노트를 허용하는 서버 정책을 확인해야 한다.
- 권장 확인: 가사 검색 UI를 sheet로 둘지 별도 화면으로 둘지 정한다.

## Open Questions

- 임시저장은 엑셀에 있으나 iOS 현재 구현 근거가 약하다. Android 이번 구현 범위에서는 제외한다.

