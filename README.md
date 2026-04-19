# Caretaker

> **Unity 기반 졸업 프로젝트** — 컴퓨터공학과 6인 팀 | 2026

---

## 프로젝트 개요

**Caretaker**는 시간 인과율 기반 2인 비대칭 협동 퍼즐 어드벤처 PC 게임입니다.

| 항목 | 내용 |
|------|------|
| 장르 | 2인 비대칭 협동 퍼즐 어드벤처 |
| 플랫폼 | PC (Windows) |
| 엔진 | Unity 6.4 |
| 네트워크 | Netcode for GameObjects (Host-Client) |
| 팀 규모 | 6인 |
| 개발 기간 | 2026.03 – 2026.11 |

---

## 빠른 시작 (팀원 온보딩)

### 1. 사전 요구사항
- [Unity 6.4](https://unity.com/releases/lts) 설치 (정확한 버전: `ProjectSettings/ProjectVersion.txt` 참고)
- [Git LFS](https://git-lfs.github.com/) 설치 및 활성화 (`git lfs --version`으로 확인)
- [JetBrains Rider](https://www.jetbrains.com/rider/) 또는 Visual Studio Code

### 2. 레포 클론
```bash
# Git LFS 초기화 (최초 1회)
git lfs install

# 레포 클론 & 에셋 다운로드
git clone https://github.com/winteryu21/Caretaker.git
cd Caretaker
git lfs pull
```

### 3. Unity에서 열기
Unity Hub → **Open** → 레포 루트 폴더 선택

### 4. Unity 에디터 설정 확인
- Edit → Project Settings → Editor:
  - **Version Control Mode**: Visible Meta Files
  - **Asset Serialization Mode**: Force Text

> ⚠️ **씬을 강제로 변경하지 마세요.** 씬 편집 전 반드시 이슈 브랜치를 생성하고 팀에 공유하세요.

---

## 디렉터리 구조

```
Caretaker/
├── docs/
│   ├── design/       # GDD, DRD
│   ├── technical/    # 아키텍처, 코딩 컨벤션
│   ├── planning/     # 마일스톤
│   └── meetings/     # 회의록
├── .gitignore
├── .gitattributes    # Git LFS 설정
├── .editorconfig
└── CONTRIBUTING.md   # 브랜치·커밋·PR 컨벤션
```

> Unity 프로젝트(`Assets/`, `ProjectSettings/`, `Packages/`)는 Unity Hub에서 프로젝트 생성 시 자동 생성됩니다.

---

## 브랜치 & 워크플로우

| 브랜치 | 역할 |
|--------|------|
| `main` | 항상 빌드 가능한 안정 버전 (직접 Push 금지) |
| `develop` | 통합 브랜치 (스프린트 단위 머지) |
| `feat/{설명}` | 기능 개발 |
| `fix/{설명}` | 버그 수정 |
| `art/{설명}` | 에셋 작업 |
| `docs/{설명}` | 문서 작업 |

자세한 컨벤션 → [`CONTRIBUTING.md`](CONTRIBUTING.md)

---

## 기술 스택

| 영역 | 선택 |
|------|------|
| 엔진 | Unity 6.4 |
| 언어 | C# 12 |
| 네트워크 | Netcode for GameObjects |
| 렌더링 | URP (2D) |
| 이슈 트래킹 | Linear |
| 버전 관리 | Git + Git LFS |

---

## 주요 문서

| 문서 | 링크 |
|------|------|
| Design Requirements Document | [`docs/design/DRD.md`](docs/design/DRD.md) |
| 코딩 컨벤션 | [`docs/technical/coding-standards.md`](docs/technical/coding-standards.md) |
| 기여 가이드 | [`CONTRIBUTING.md`](CONTRIBUTING.md) |

---

## 팀 구성

| 이름 | 주 담당 |
|------|--------|
| 유민서 | PM · 기획 |
| 김도경 | 아트 · 비주얼 |
| 김병규 | 레벨 디자인 · UI |
| 백승헌 | 시간 인과 시스템 · AI |
| 배원일 | 네트워크 · 인프라 |
| 권순표 | 플레이어 제어 · QA |

---

*Caretaker © 2026 Team. Graduation Project.*
