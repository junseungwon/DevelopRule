# DevelopRule — 프로젝트 컨텍스트

## 하네스: Unity 기능 개발

**목표**: Unity 신규 기능 하나를 기획부터 QA 완료까지 6명의 전문 에이전트 팀이 협업하여 완성한다.

---

## 에이전트 팀

| 에이전트 | 역할 |
|---------|------|
| planner | 기능 명세서 + 기술 스펙 + 작업 분배 |
| code-developer | Unity C# 스크립트·컴포넌트 구현 |
| vfx-effector | 파티클·셰이더·VFX Graph 에셋 제작 |
| scene-developer | 샘플씬 계층 구조·프리팹·라이팅 구성 |
| code-reviewer | C# 코드 규칙 준수·품질 검토 |
| qa-inspector | 정적 체크리스트 + 씬 실행 로그 최종 검증 |

---

## 스킬

| 스킬 | 용도 | 사용 에이전트 |
|------|------|-------------|
| unity-feature-orchestrator | Unity 신규 기능 전체 파이프라인 조율 | 전체 팀 |

---

## 실행 규칙

- Unity 기능 개발 요청 시 `unity-feature-orchestrator` 스킬을 통해 에이전트 팀으로 처리하라
- 단순 질문·코드 설명은 에이전트 팀 없이 직접 응답해도 무방
- 모든 에이전트는 `model: "opus"` 사용
- 중간 산출물: `_workspace/` 디렉토리

### 코드 규칙 참조 경로

| 규칙 | 파일 |
|------|------|
| 스타일·포맷 | `Develop/Code/core-Code/code-style.md` |
| Unity 패턴·금지 패턴 | `Develop/Code/core-Code/code-pattern.md` |
| 핵심 규칙 요약 | `Develop/Code/core-Code/main-code-rules.md` |
| 코드 제작 3단계 프로세스 | `Develop/Code/core-Code/code-main.mdc` |

---

## 디렉토리 구조

```
.cursor/rules/
├── agents/
│   ├── planner.mdc
│   ├── code-developer.mdc
│   ├── vfx-effector.mdc
│   ├── scene-developer.mdc
│   ├── code-reviewer.mdc
│   └── qa-inspector.mdc
└── skills/
    └── unity-feature-orchestrator/
        └── SKILL.mdc
```

---

## 변경 이력

| 날짜 | 변경 내용 | 대상 | 사유 |
|------|----------|------|------|
| 2026-04-08 | 초기 구성 | 전체 | Unity 기능 개발 하네스 신규 구축 |
| 2026-04-08 | 룰 파일 확장자 정리 | `.cursor/rules` | Cursor rules 운영 형식(`.mdc`)으로 통일 |
