---
title: "최소주의 Git Markdown SSOT와 멀티아키 로컬 K8s 이식성 설계 원칙"
created: 2026-10-31
updated: 2026-10-31
tags: [architecture, automation, kubernetes, obsidian, portability]
status: seed
visibility: public
---

# 최소주의 Git Markdown SSOT와 멀티아키 로컬 K8s 이식성 설계 원칙

## 요약

개인 반자동 파이프라인을 구축할 때 외부 SaaS·DB 의존성을 제거하고 Git/Obsidian Markdown을 단일 진실 공급원(SSOT)으로 삼으면, 운영 복잡도와 이기종 하드웨어(`amd64` ↔ `arm64`) 마이그레이션 비용을 동시에 0에 가깝게 줄일 수 있다.

## 내용

1. **선행 계획과 시간 단축 지표 중심 설계**: 초기 수동 프로토타입 단계부터 최종 반자동(Human-in-the-Loop) 단계까지의 단계별 소요 시간 단축 경로를 먼저 고정하고, 포맷이 검증된 뒤 자동화 코드를 도입해 조기 최적화를 피한다.
2. **외부 DB 제로 — Markdown Frontmatter 상태 머신**: 카탈로그·초안·승인 큐 상태를 YAML frontmatter(`inbox -> draft -> approved -> published`)로 관리하면 별도 백엔드 DB 백업 없이 `git clone`만으로 전체 상태가 복원된다.
3. **CI 정적 가드레일과 아웃바운드 수집**: 인바운드 포트 개방이 불필요한 WebSocket 기반 수집 워커와 필수 고지문·표기 오류를 선제 차단하는 정적 검증기를 결합해 홈 클러스터 보안과 품질을 동시에 확보한다.
4. **Windows WSL2(`amd64`) → Apple Silicon(`arm64`) 이식성 계약**: x86/특정 GPU 종속 바이너리를 배제하고 Kustomize `base` + OS별 `overlays` 구조를 유지해 호스트 전환 시 재설계를 방지한다.

## 관련 자료

- 워크스페이스 `CONCEPTS.md` (`Host Target`, `Guest Target`)
