# Notes

Obsidian으로 바로 열 수 있는 공개 Markdown vault다. 저장소 이름에는 과거의
`notes-private`가 남아 있지만, 모든 tracked content는 처음부터 공개 가능한 자료로
취급한다.

## 구조

- `inbox/`: 아직 분류하지 않은 빠른 기록
- `notes/`: 정리된 개인 지식
- `assets/`: 공개 가능한 첨부파일
- `publish/`: 블로그 승격 후보 초안. 이 폴더에 있다고 자동 게시되지는 않는다.
- `_templates/`: 노트 템플릿

## 작성 규칙

- 가능한 한 표준 Markdown 링크를 사용한다.
- token, private key, password, 복구 코드, 내부 host와 개인 로컬 경로를 저장하지 않는다.
- 새 파일과 첨부파일은 commit 전에 공개 가능한지 확인한다.
- 블로그 글은 검토 후 `blogs/`의 별도 branch와 PR 절차로 수동 승격한다. 이 vault를
  블로그 build input이나 content loader로 직접 연결하지 않는다.
- 이 저장소는 `zWorkspaces`의 `notes/` 서브모듈로 사용한다.

## 새 노트

`_templates/note.md`를 복사해 `inbox/` 또는 `notes/`에서 시작한다. Obsidian의
Templates 플러그인을 쓸 경우 template folder를 `_templates`로 지정한다.
