# Private Notes

Obsidian으로 바로 열 수 있는 비공개 Markdown vault다. 이 저장소는 공개 블로그나
workspace의 build input으로 연결하지 않는다.

## 구조

- `inbox/`: 아직 분류하지 않은 빠른 기록
- `notes/`: 정리된 개인 지식
- `assets/`: 비공개 첨부파일
- `publish/`: 공개 후보 초안. 이 폴더에 있다고 자동 공개되지는 않는다.
- `_templates/`: 노트 템플릿

## 작성 규칙

- 가능한 한 표준 Markdown 링크를 사용한다.
- token, private key, password, 복구 코드 같은 credential은 노트에 저장하지 않는다.
- 공개할 글은 민감 정보와 private 링크를 제거해 별도 blog branch로 수동 승격한다.
- 공개 저장소에는 이 vault의 remote, 로컬 경로, symlink, submodule을 추가하지 않는다.

## 새 노트

`_templates/note.md`를 복사해 `inbox/` 또는 `notes/`에서 시작한다. Obsidian의
Templates 플러그인을 쓸 경우 template folder를 `_templates`로 지정한다.
