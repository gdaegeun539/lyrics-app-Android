# 노트 상세/댓글 PRD

## 범위

노트 상세 조회, 관련 댓글 목록, 댓글 작성/삭제, 노트 상세 내 좋아요/북마크/메뉴, 링크 웹뷰 처리를 정의한다.

## Android 화면명

- 기존: `NoteDetailScreen`, `InternalWebViewScreen`
- 추가 구현 대상: `ReportScreen`

## 요구사항

- 노트 상세는 작성자, 작성 시간, 본문, 가사, 곡 정보, 좋아요 수, 댓글 수, 북마크 상태를 표시한다.
- 본문 링크는 선택 가능해야 하며 내부 웹뷰 또는 외부 링크 정책으로 열린다.
- 댓글 목록은 작성 순서 또는 iOS 구현 정렬에 맞춰 표시한다.
- 사용자는 댓글을 작성할 수 있다.
- 사용자는 본인이 작성한 댓글을 삭제할 수 있다.
- 댓글 메뉴에서 타인 댓글 신고 또는 작성자 차단 진입을 제공한다.
- 노트 상세에서도 좋아요/북마크 상태 변경을 지원한다.

## 사용자 흐름

1. 피드 또는 검색 결과에서 노트를 선택해 `NoteDetailScreen`으로 이동한다.
2. 노트 상세와 댓글 목록을 조회한다.
3. 댓글 입력창에 내용을 입력하고 등록한다.
4. 댓글 등록 성공 시 댓글 목록과 댓글 수가 갱신된다.
5. 본인 댓글 삭제 선택 시 확인 후 삭제한다.
6. 신고 선택 시 `ReportScreen`으로 이동한다.
7. 본문 링크 선택 시 `InternalWebViewScreen`으로 이동한다.

## 상태/이벤트

- 상세 진입 시 `getNoteWithComments`로 노트와 댓글을 함께 조회한다.
- 댓글 작성은 빈 값 또는 글자수 제한 초과를 막는다.
- 댓글 삭제 성공 시 목록에서 제거하고 댓글 수를 갱신한다.
- 좋아요/북마크는 상세 화면 상태를 낙관적으로 갱신하고 실패 시 원복한다.
- 네트워크 실패 시 상세 화면은 오류 상태를 표시하고 재시도 가능해야 한다.

## API 요약

- `getNoteWithComments`: GET `/api/v1/notes/{noteID}`
- `postComment`: POST `/api/v1/comments`
  - 주요 body: note id, comment body
- `deleteComment`: DELETE `/api/v1/comments/{commentID}`
- `postLikes` / `deleteLikes`: POST/DELETE `/api/v1/likes`
- `postBookmarks` / `deleteBookmarks`: POST/DELETE `/api/v1/bookmarks`
- `reportNote`: POST `/api/v1/reports`
- `postBlockUserProfile`: POST `/api/v1/blocks`

## 수용 기준

- 노트 상세 진입 시 노트 본문과 댓글 목록을 표시한다.
- 댓글 입력값이 비어 있으면 등록할 수 없다.
- 댓글 등록 성공 시 새 댓글이 목록에 반영된다.
- 본인 댓글에만 삭제 액션이 표시된다.
- 댓글 삭제 확인 후 성공하면 댓글이 목록에서 제거된다.
- 좋아요/북마크 상태 변경 실패 시 이전 상태로 복구된다.
- 링크가 포함된 본문을 선택하면 웹뷰 정책에 따라 열린다.
- 비로그인 사용자가 댓글 작성/좋아요/북마크를 시도하면 로그인 필요 상태로 처리한다.

## iOS Reference

- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Note/NoteCommentsViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/Note/NoteDetailViewController.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/NoteCommentsViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewModels/NoteDetailViewModel.swift`
- `app-iOS/Projects/Feature/Home/Interface/Sources/ViewControllers/CommentMenuViewController.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/GetNoteWithCommentsUseCase.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/WriteCommentUseCase.swift`
- `app-iOS/Projects/Domain/Note/Interface/Sources/UseCases/DeleteCommentUseCase.swift`

## 기획 명세 출처

- 커뮤니티 피드 화면 / 게시글 세부 / 게시글 내용 확인
- 커뮤니티 피드 화면 / 게시글 세부 / 댓글 목록
- 커뮤니티 피드 화면 / 게시글 세부 / 댓글 작성
- 커뮤니티 피드 화면 / 게시글 세부 / 댓글 삭제
- 비로그인 유저용 커뮤니티 피드 화면 / 게시글 세부

## Android Decision Needed

- 필수 확인: 댓글 목록 정렬을 엑셀의 작성순으로 둘지 서버 반환순으로 둘지 확정해야 한다.
- 필수 확인: 본문 텍스트 복사 금지 요구를 Android에서 적용할지 결정해야 한다.
- 권장 확인: 링크 클릭 시 내부 웹뷰를 기본으로 할지 외부 브라우저 선택을 허용할지 정한다.

## Open Questions

- 댓글 글자수 제한 1,000자는 엑셀 기준이다. 서버 제한과 iOS 선제 제한 구현 여부 확인이 필요하다.

