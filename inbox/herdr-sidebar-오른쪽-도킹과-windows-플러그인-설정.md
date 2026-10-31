---
title: "Windows에서 herdr-sidebar 기본 오른쪽 도킹 설정과 즉시 재배치"
created: 2026-10-31
updated: 2026-10-31
tags:
  - herdr
  - herdr-sidebar
  - windows
  - terminal
status: seed
visibility: public
---

# Windows에서 herdr-sidebar 기본 오른쪽 도킹 설정과 즉시 재배치

## 요약

Herdr의 `herdr-sidebar` 플러그인은 호스트 설정(`config.toml`)이 아니라 플러그인 전용 상태 디렉터리(`HERDR_PLUGIN_STATE_DIR`) 아래 `state.json`에 도킹 방향(`dock_right`) 등의 영구 설정을 저장한다. Windows에서는 `%LOCALAPPDATA%\herdr\plugins\herdr-sidebar\state.json`의 `"dock_right": true`를 갱신한 뒤, `-windows` 접미사가 붙은 `refresh-sidebars-windows` 액션을 호출하면 기존 작업 패인 포커스를 유지한 채 열린 사이드바도 즉시 오른쪽으로 재배치할 수 있다.

## 내용

### 1. Windows에서의 Herdr 설정·플러그인 경로 구분

Herdr는 호스트 설정, 플러그인 설치 체크아웃, 플러그인 런타임 영구 상태 디렉터리를 분리해 관리한다.

| 구분 | Windows 경로 | macOS / Linux 경로 |
| :--- | :--- | :--- |
| 호스트 설정 | `%APPDATA%\herdr\config.toml` | `~/.config/herdr/config.toml` |
| 플러그인 레지스트리 및 소스 | `%APPDATA%\herdr\plugins.json`, `%APPDATA%\herdr\plugins\github\` | `~/.config/herdr/plugins.json`, `~/.config/herdr/plugins/github/` |
| 플러그인 영구 상태 (`HERDR_PLUGIN_STATE_DIR`) | `%LOCALAPPDATA%\herdr\plugins\<plugin-id>\` | `${XDG_STATE_HOME:-~/.local/state}/herdr/plugins/<plugin-id>/` |

호스트 `config.toml`을 수정한 경우에는 서버 재시작 없이 `herdr server reload-config`로 반영한다.

### 2. `herdr-sidebar` 기본 오른쪽 도킹 설정

1. `%LOCALAPPDATA%\herdr\plugins\herdr-sidebar\state.json`에서 `"dock_right"` 값을 `true`로 변경한다.
   - 파일 인코딩은 **BOM 없는 UTF-8**을 유지해야 한다(Windows PowerShell 5.1의 `Set-Content` 기본 인코딩이나 UTF-8 BOM을 쓰면 파싱 오류가 날 수 있으므로 주의한다).
   - 실행 중인 `herdr-sidebar` 프로세스는 `state.lock` 파일 잠금 기반의 read-modify-write(`update_state`)로 개별 필드만 갱신하고 주기적으로 공유 설정을 다시 읽으므로, 나머지 키를 보존한 채 `"dock_right": true`만 수정하면 실행 중 프로세스에 의해 덮어써지지 않는다.
   - 사이드바 TUI 내부에서 직접 바꿀 때는 `s` 키(⚙ Settings) → **Dock on the right** 항목을 토글해도 동일하게 `state.json`이 갱신된다.

### 3. 실행 중인 세션에 무중단 즉시 반영

`state.json`만 바꾸면 새로 열리는 탭·워크스페이스부터 적용된다. 현재 열려 있는 탭의 사이드바까지 작업 패인이나 프리뷰 패인을 닫지 않고 즉시 오른쪽으로 옮기려면 플러그인 리프레시 액션을 실행한다.

Windows에서는 매니페스트 액션 ID가 플랫폼별로 고유해야 하므로 `-windows` 접미사가 붙는다(`open-sidebar-windows`, `refresh-sidebars-windows`, `redeploy-windows` 등).

```powershell
# 실행 중인 사이드바에 Ctrl+Q(SCM 초안 저장 후 종료)를 보내고 새 도킹 위치로 재오픈
herdr plugin action invoke refresh-sidebars-windows --plugin herdr-sidebar

# 현재 패인 기준 레이아웃 좌표(x, width)와 포커스 유지 여부 확인
herdr pane layout --pane $env:HERDR_PANE_ID
```

macOS / Linux에서는 `-windows` 접미사 없이 `herdr plugin action invoke refresh-sidebars --plugin herdr-sidebar`를 사용한다.

## 관련 자료

- [Herdr 공식 문서](https://herdr.dev)
- [alexarthurs/herdr-sidebar GitHub 저장소](https://github.com/alexarthurs/herdr-sidebar)
