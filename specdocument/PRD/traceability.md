# Traceability

## 기준

- 엑셀: `Feelin 기능명세서.xlsx` / `기능명세서(260430 수정완료)` 시트
- iOS: `app-iOS` 현재 구현
- Android: `lyrics-app-Android` 현재 화면명을 우선 사용

## 도메인 연결표

| 엑셀 테마 | 주요 에픽/기능 | PRD 문서 | Android 화면명 | iOS reference |
|---|---|---|---|---|
| 온보딩 | 로그인, 약관 동의, 회원정보 수집, 프로필 설정 | `onboarding-auth.md` | `LoginScreen`, `OnboardingTermsScreen`, `OnboardingGenderAgeScreen`, `ProfileScreen`, `WelcomeScreen` | `LoginViewController`, `UseAgreementViewController`, `UserInformationViewController`, `ProfileViewController`, `WelcomeViewController`, `LoginViewModel`, `ProfileViewModel` |
| 홈화면(좌측탭) | 사용자 맞춤 선택, 관심 아티스트, 최신 게시글 피드 | `home-favorite-artist.md`, `community-note-feed.md` | `HomeScreen`, `CommunityMainScreen`, `OnboardingFavoriteArtistScreen` | `HomeViewController`, `ArtistSelectViewController`, `SearchMoreFavoriteArtistViewController`, `HomeViewModel`, `ArtistSelectViewModel` |
| 비로그인 유저용 홈화면(좌측탭) | 아티스트 찾아보기, 최신 게시글 피드 제한 | `home-favorite-artist.md`, `common-policies.md` | `HomeScreen`, `CommunityMainScreen` | `HomeViewController`, `HomeViewModel` |
| 곡 검색(중앙탭) | 곡 검색, 곡 선택, 게시글 필터 | `search.md` | `NoteSearchScreen`, `NoteSearchResultScreen`, `NoteDetailScreen` | `SearchNoteViewController`, `SearchNoteViewModel`, `GetSearchedNotesUseCase`, `GetSongNotesUseCase` |
| 비로그인 유저용 곡 검색(중앙탭) | 곡 검색, 곡 선택, 로그인 필요 액션 제한 | `search.md`, `common-policies.md` | `NoteSearchScreen`, `NoteSearchResultScreen`, `NoteDetailScreen` | `SearchNoteViewController`, `SearchNoteViewModel` |
| 알림화면 | 내 소식 알림, 전체 알림 | `notification.md` | `NotificationScreen` | `NoteNotificationContainerViewController`, `NotePersonalNotificationViewController`, `NotePublicNotificationViewController`, `NotePersonalNotificationViewModel`, `NotePublicNotificationViewModel` |
| 아티스트 소개 화면 | 전체 취소선 | `future-or-excluded.md` | 해당 없음 | 해당 없음 |
| 비로그인 유저용 아티스트 소개 화면 | 전체 취소선 | `future-or-excluded.md` | 해당 없음 | 해당 없음 |
| 커뮤니티 피드 화면 | 커뮤니티 정보, 게시글 필터, 좋아요, 댓글, 북마크, 글쓰기 | `community-note-feed.md`, `note-detail-comment.md`, `note-write-edit.md` | `CommunityMainScreen`, `NoteDetailScreen`, `NoteFormScreen` | `CommunityMainViewController`, `CommunityMainViewModel`, `NoteDetailViewController`, `NoteCommentsViewController`, `PostNoteViewController`, `EditNoteViewController` |
| 비로그인 유저용 커뮤니티 피드 화면 | 커뮤니티 조회, 게시글 조회, 로그인 필요 액션 제한 | `community-note-feed.md`, `note-detail-comment.md`, `common-policies.md` | `CommunityMainScreen`, `NoteDetailScreen` | `CommunityMainViewController`, `NoteDetailViewController` |
| 게시글 작성 화면 | 곡 추가, 가사 입력, 가사 배경 선택, 본문 작성 | `note-write-edit.md` | `NoteFormScreen`, `SearchSongScreen` | `PostNoteViewController`, `EditNoteViewController`, `SearchSongViewController`, `LyricsBackgroundViewController`, `PostNoteViewModel`, `EditNoteViewModel`, `SearchSongViewModel` |
| 행사 정보(오른쪽에서 두번째 탭) | 행사 정보, 행사 상세정보 | `event-banner.md`, `future-or-excluded.md` | `HomeScreen`, 필요 시 `EventDetailScreen` | `HomeViewModel`, `FetchSingleEventUseCase`, `FetchBannersUseCase`, `RefuseEventUseCase` |
| 마이페이지 | 설정, 내 노트, 북마크, 회원정보 수정 | `mypage-profile.md`, `report-block-account.md` | `MyPageScreen`, `SettingScreen`, `UserInfoScreen`, `EditProfileScreen`, `BlockedUsersScreen`, `DeleteAccountScreen` | `MyPageViewController`, `SettingViewController`, `UserProfileViewController`, `MyNoteViewController`, `BookmarkViewController`, `BlockedUsersViewController`, `DeleteUserViewController` |
| 비로그인 유저용 마이페이지 | 설정, 서비스 링크, 버전 정보, 로그인 유도 | `mypage-profile.md`, `common-policies.md` | `MyPageScreen`, `SettingScreen`, `InternalWebViewScreen` | `MyPageViewController`, `SettingViewController`, `InternalWebViewController` |

## API 연결표

| 기능군 | iOS `FeelinAPI` case | Method/Path | PRD 문서 |
|---|---|---|---|
| 로그인 | `login` | POST `/api/v1/auth/sign-in` | `onboarding-auth.md` |
| 회원가입 | `signUp` | POST `/api/v1/auth/sign-up` | `onboarding-auth.md` |
| 토큰 검증 | `checkUserValidity` | GET `/api/v1/auth/validate-token` | `onboarding-auth.md`, `common-policies.md` |
| 토큰 재발급 | `reissueAccessToken` | POST `/api/v1/auth/token` | `common-policies.md` |
| 아티스트 목록 | `getArtists` | GET `/api/v1/artists` | `home-favorite-artist.md` |
| 아티스트 검색 | `searchArtists` | GET `/api/v1/artists/search` | `home-favorite-artist.md` |
| 관심 아티스트 | `getFavoriteArtists`, `postFavoriteArtist`, `deleteFavoriteArtist`, `postFavoriteArtists` | GET/POST/DELETE `/api/v1/favorite-artists`, POST `/api/v1/favorite-artists/batch` | `home-favorite-artist.md`, `community-note-feed.md` |
| 홈 노트 | `getFavoriteArtistsRelatedNotes` | GET `/api/v1/notes` | `home-favorite-artist.md` |
| 아티스트 노트 | `getArtistNotes` | GET `/api/v1/notes/artists` | `community-note-feed.md` |
| 곡 검색 | `searchSongs`, `getSearchedNotes` | GET `/api/v1/songs/search/artists`, GET `/api/v1/songs/search` | `note-write-edit.md`, `search.md` |
| 곡 노트 | `getSongNotes`, `getSongDetail` | GET `/api/v1/notes/songs`, GET `/api/v1/songs/{songID}` | `search.md` |
| 노트 상세/댓글 | `getNoteWithComments`, `postComment`, `deleteComment` | GET `/api/v1/notes/{noteID}`, POST `/api/v1/comments`, DELETE `/api/v1/comments/{commentID}` | `note-detail-comment.md` |
| 노트 작성/수정/삭제 | `postNote`, `patchNote`, `deleteNote` | POST `/api/v1/notes`, PATCH/DELETE `/api/v1/notes/{noteID}` | `note-write-edit.md`, `community-note-feed.md` |
| 좋아요 | `postLikes`, `deleteLikes` | POST/DELETE `/api/v1/likes` | `community-note-feed.md`, `note-detail-comment.md`, `search.md` |
| 북마크 | `postBookmarks`, `deleteBookmarks` | POST/DELETE `/api/v1/bookmarks` | `community-note-feed.md`, `note-detail-comment.md`, `mypage-profile.md` |
| 알림 | `getPersonalNotifications`, `getPublicNotifications`, `getHasUncheckedNotification`, `checkNotification` | GET `/api/v1/notifications/personal`, GET `/api/v1/notifications/public`, GET `/api/v1/notifications/check`, PATCH `/api/v1/notifications/{notificationID}` | `notification.md` |
| 사용자 프로필 | `getUserProfile`, `patchUserProfile` | GET/PATCH `/api/v1/users` | `mypage-profile.md` |
| 내 노트/북마크 | `getMyNotes`, `getMyNotesByBookmark` | GET `/api/v1/notes`, GET `/api/v1/notes/bookmarked` | `mypage-profile.md` |
| 신고 | `reportNote` | POST `/api/v1/reports` | `report-block-account.md` |
| 차단 | `postBlockUserProfile`, `deleteBlockUserProfile`, `getBlockedUsers` | POST/DELETE `/api/v1/blocks`, GET `/api/v1/users/blocks` | `report-block-account.md`, `mypage-profile.md` |
| 회원탈퇴 | `deleteUser` | DELETE `/api/v1/auth/delete` | `report-block-account.md` |
| 이벤트/배너 | `getEvents`, `postEventRefuse`, `getBanners` | GET `/api/v1/events`, POST `/api/v1/events/refuse`, GET `/api/v1/banners` | `event-banner.md` |

## Android 현재 구현 참고

- Android 현재 활성 온보딩 흐름은 `login -> terms -> genderage -> profile -> welcome`이다.
- `onboarding/favoriteartist`는 구현 파일이 있으나 active nav flow에 연결되어 있지 않다.
- Android에는 이미 `OnboardingFavoriteArtistScreen`, `HomeScreen`, `CommunityMainScreen`, `NotificationScreen`, `NoteSearchScreen`, `NoteSearchResultScreen`, `NoteDetailScreen`, `NoteReportScreen`, `NoteFormScreen`, `SearchSongScreen`, `MyPageScreen`, `SettingScreen`, `UserInfoScreen`, `EditProfileScreen`, `BlockedUsersScreen`이 존재한다.
- 알림, 신고, 차단 목록 화면은 Android에 구현되어 있으며, 회원탈퇴 화면은 추가 구현 대상으로 남아 있다.
