# AGENTS.md — 공개 노트 가드레일

이 저장소의 모든 tracked content는 공개 자료로 취급한다.

## 공개 경계

- credential, token, private key, password, 복구 코드, 내부 host, 개인 로컬 경로와
  공개하면 안 되는 원문을 commit하지 않는다.
- 새 파일, frontmatter, 첨부파일과 전체 diff를 commit 전에 확인한다.
- `.obsidian/workspace*`, `.trash/`, `.env*`, `.DS_Store`와 credential 파일을 추적하지
  않는다.

## 작성 규칙

- 표준 Markdown 링크를 우선한다.
- 새 노트는 `_templates/note.md`를 기준으로 `inbox/` 또는 `notes/`에 작성한다.
- `publish/`는 블로그 후보일 뿐 자동 게시 승인이 아니다.
- 블로그는 이 저장소를 symlink, build input 또는 content loader로 연결하지 않는다.
  검토한 문서만 `zWorkspaces`의 `runbooks/public-notes-publishing.md`와 `blogs/`
  저장소 규약에 따라 별도 branch로 승격한다.

## Git 규칙

- child 저장소 변경을 먼저 검증하고 반영한 뒤 상위 `zWorkspaces`의 `notes` gitlink를
  갱신한다.
- 기존 사용자 변경과 미추적 파일을 범위 밖에서 수정하거나 삭제하지 않는다.
- 공개 여부를 이유로 secret 검사를 생략하지 않는다.

## 일반 원칙

- 한국어로 응답한다. 사용자가 영어로 물으면 영어로 응답한다.
