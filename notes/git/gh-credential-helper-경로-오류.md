---
title: "gitconfig의 gh credential helper 경로가 낡았을 때 git push 대기 해결하기"
created: 2026-10-16
updated: 2026-10-16
tags:
  - git
  - github-cli
  - troubleshooting
status: seed
visibility: public
---

# gitconfig의 gh credential helper 경로가 낡았을 때 git push 대기 해결하기

## 요약

Homebrew 설치 경로가 바뀐 뒤 `~/.gitconfig`에 이전 `gh` 바이너리 경로가 남아 있으면, HTTPS `git push` 시 인증 헬퍼 실행에 실패하고 `Username for 'https://github.com':` 입력 대기에 걸린다. `gh auth setup-git`으로 경로를 갱신하거나 `-c credential.helper` 일회성 옵션으로 우회할 수 있다.

## 내용

### 증상 (Symptoms)

HTTPS 원격 저장소로 `git push`를 실행할 때 아래와 같은 오류 출력 후 프롬프트 입력 대기 상태에 멈춘다.

```text
<old-brew-prefix>/bin/gh auth git-credential get: <old-brew-prefix>/bin/gh: No such file or directory
Username for 'https://github.com':
```

비대화형(백그라운드) 실행 환경에서는 표준 입력을 기다리며 명령이 끝나지 않는다.

### 원인 (Root Cause)

`gh auth login` 또는 `gh auth setup-git` 실행 시점의 `gh` 절대 경로가 `~/.gitconfig`의 `credential.https://github.com.helper` 항목에 기록된다. 이후 Homebrew 경로를 이전(예: 커스텀 프리픽스 → `/opt/homebrew`)하면 기존 절대 경로의 바이너리가 사라져 Git이 credential helper 호출에 실패하고 기본 프롬프트로 폴백한다.

```bash
git config --list --show-origin | grep credential
```

### 해결 (Solution)

1. **영구 반영 (`~/.gitconfig` 갱신)**
   현재 `PATH`에 잡힌 `gh` 기준으로 Git credential helper 설정을 다시 쓴다.
   ```bash
   gh auth setup-git
   ```

2. **일회성 실행 (설정 파일 수정 없이 즉시 푸시)**
   기존 helper 목록을 비운 뒤 현재 `which gh` 경로를 지정해 푸시한다.
   ```bash
   git -c credential.helper="" \
       -c credential.helper="!$(which gh) auth git-credential" \
       push origin main
   ```

## 관련 자료

- [워크트리 만들기](./워크트리%20만들기.md)
- [서브모듈 관리하기](./서브모듈%20관리하기.md)
