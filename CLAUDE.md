# Claude Code 전역 규칙 인덱스 (연결 허브)
> **⭐ 시작 시 먼저 읽기**: [Rule/Always/Always.md](Rule/Always/Always.md)
> 모든 작업의 시작점입니다. 항상 이 파일을 먼저 읽고 진행하세요.

---
## 📌 주요 규칙 링크
- [Rule/Always/Always.md](Rule/Always/Always.md) — 항상 적용되는 기본 규칙 (필수 ⭐)
---

## DevelopRule 디렉토리 구조

```
C:\DevelopRule\
├── .git/
├── agents/                                    — 에이전트 정의 저장소
│   ├── general-agents/
│   │   ├── hub.md                            — 에이전트 인덱스
│   │   ├── public-agents/                    — 공용 에이전트 (researcher, code-reviewer 등)
│   │   └── unity-agents/                     — Unity 전문 에이전트 (architect, developer 등)
│   ├── github-agents/                        — GitHub 분석 에이전트
│   │   ├── collector/                        — repo-fetcher
│   │   ├── analyzers/                        — code-analyzer, dependency-analyzer, git-historian 등
│   │   ├── orchestrator/                     — hub-github (분석 코디네이터)
│   │   └── reporter/                         — github-reporter, visualizer
│   └── rule-developer/                       — 규칙 설계 및 검증 하네스 ⭐
│       ├── AGENT.md                          — 메인 정의
│       ├── ORCHESTRATOR.md                   — 4단계 파이프라인 (Phase 1-4)
│       ├── analyzer/                         — Phase 1: 코드 분석 (Unity 규칙 42개 통합)
│       ├── designer/                         — Phase 2: 규칙 설계
│       ├── spec-generator/                   — Phase 3: 에이전트 명세 생성
│       └── validator/                        — Phase 4: 규칙 점검
├── plugins/
│   ├── harness/                              — Agent Team & Skill Architect
│   │   ├── skills/harness/                   — /harness 스킬 (에이전트 생성)
│   │   ├── skills/harness/references/        — 에이전트 설계 가이드
│   │   └── README_KO.md
│   └── Claude-Code-Usage-Monitor/            — Claude 사용량 모니터링
├── Rule/                                      — 프로젝트별 규칙 (NEW ✨)
│   └── Always/
│       └── Always.md                         — 항상 적용되는 기본 규칙 (필수)
├── rules/                                     — 기본 규칙 허브
│   ├── setup/                                — 작업 시작 전 초반 세팅 규칙
│   ├── always/                               — 항상 적용되는 기본 규칙 (레거시)
│   ├── details/                              — 세부 규칙 (Bash, 코드, 주석, 디버그)
│   └── forbidden/                            — 금지사항 규칙
├── CLAUDE.md                                 — 이 문서 (규칙 인덱스 & 연결 허브)
└── README.md
```
