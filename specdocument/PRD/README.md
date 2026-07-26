# Feelin Android 구현 PRD

## 문서 목적

이 PRD는 Android 앱 전체 구현을 위해 iOS 현재 구현을 기준으로 제품 요구사항을 재정리한 개발자/AI agent용 문서다. `Feelin 기능명세서.xlsx`는 보조 입력으로 사용하며, iOS 구현과 충돌하거나 아직 구현 근거가 없는 항목은 본문이 아니라 `future-or-excluded.md` 또는 각 문서의 `Open Questions`, `Android Decision Needed`에 분리한다.

## 분석 기준

- 작성 기준일: 2026-07-02
- 구현 기준: `app-iOS` 현재 코드
- 기획 명세 기준: `lyrics-app-Android/specdocument/Feelin 기능명세서.xlsx`의 `기능명세서(260430 수정완료)` 시트
- 제외 기준: 어드민 시트, 전체 취소선 기능, 일부 취소선 문장, iOS 구현 근거가 없는 추후 기능

## 문서 규칙

- iOS 구현을 source of truth로 둔다.
- Android에 이미 존재하는 화면은 현재 코드의 화면명을 사용한다.
- Android에 아직 없는 화면은 기능 중심 임시 화면명을 사용한다.
- API는 iOS `FeelinAPI` enum case와 method/path 요약만 적고 DTO 전체 구조는 복붙하지 않는다.
- 수용 기준은 체크리스트형으로 작성한다.
- 별도 검증 섹션은 두지 않는다.
- `Open Questions`는 제품 정책/기획/요구사항 불명확성이다.
- `Android Decision Needed`는 Android 구현 방식 선택이며 `필수 확인`, `권장 확인`만 사용한다.

## 문서 목록

- `common-policies.md`: 공통 정책, 상태, 오류, 용어
- `onboarding-auth.md`: 로그인, 약관, 성별/출생연도, 프로필, 웰컴
- `home-favorite-artist.md`: 홈, 배너, 관심 아티스트, 초기 아티스트 선택
- `community-note-feed.md`: 커뮤니티 피드, 필터, 좋아요, 북마크, 삭제, 차단
- `note-detail-comment.md`: 노트 상세, 댓글 목록/작성/삭제, 링크 웹뷰
- `note-write-edit.md`: 노트 작성/수정, 곡 검색, 가사/본문/배경
- `search.md`: 곡 기반 노트 검색, 검색 결과 피드
- `notification.md`: 개인/전체 알림, 읽음 처리
- `mypage-profile.md`: 마이페이지, 내 노트, 북마크, 프로필/설정
- `report-block-account.md`: 신고, 차단, 로그아웃, 회원탈퇴
- `event-banner.md`: 홈 배너, 이벤트 팝업/거절
- `future-or-excluded.md`: 본문 제외/추후/폐기 항목
- `traceability.md`: 엑셀 테마/에픽/기능과 iOS reference, PRD 연결표

## 우선순위 표기

- Critical: 엑셀 1단계, 핵심 사용 흐름에 필요한 기능
- Essential: 엑셀 2단계, 핵심 기능을 보조하는 기능
- Additional: 엑셀 3단계, 편의/확장 기능
