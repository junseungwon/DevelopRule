# Rule Developer Agent (규칙 제작자 에이전트)

## 📋 에이전트 정의

| 속성 | 값 |
|------|-----|
| **에이전트명** | rule-developer |
| **역할** | GitHub 코드 분석 → 규칙 설계 → 에이전트 명세 생성 |
| **타입** | 전문 설계 에이전트 |
| **입력** | GitHub URL, 분석 대상 패턴/도메인 |
| **출력** | 규칙 정의, 에이전트 명세, 스킬 목록 |

---

## 🎯 에이전트 책임

### Phase 1: GitHub 코드 분석
```
입력: GitHub URL
↓
- 저장소 구조 분석
- 코드 스타일 파악
- 디렉토리/네이밍 규칙 추출
- 주요 패턴 식별
↓
출력: code-analysis-report.md
```

### Phase 2: 규칙 설계
```
입력: code-analysis-report.md
↓
- 규칙 명세 설계
- 적용 범위/조건 정의
- 조치(action) 결정
↓
출력: rule-design-spec.md
```

### Phase 3: 에이전트 명세 생성
```
입력: rule-design-spec.md
↓
- 개발자 에이전트 요구사항 정의
- 필요 스킬 목록 작성
- 에이전트 구조 명세
- 워크플로우 정의
↓
출력: developer-agent-spec.md
```

### Phase 4: 규칙 명세 검증
```
입력: developer-agent-spec.md
↓
- 규칙 명확성 검증
- 실행 가능성 검증
- 정합성 검증 (에이전트-스킬-워크플로우)
- 문제점 분류 및 권고
↓
출력: validation-report.md + recommendations.md
```

---

## 📦 에이전트 입출력

### 입력
```markdown
# GitHub URL
https://github.com/owner/repo

# 분석 대상
- 패턴: async/await, null-safety, dependency-injection 등
- 도메인: C#, TypeScript, Python 등
- 계층: Data Layer, Business Logic, UI 등
```

### 출력 (산출물)

#### 1. code-analysis-report.md
```markdown
# 코드 분석 보고서

## 저장소 정보
- 기술 스택
- 디렉토리 구조
- 파일 네이밍 규칙

## 코드 스타일
- 들여쓰기, 괄호 규칙
- 네이밍 컨벤션 (camelCase, snake_case 등)
- 주석 스타일

## 식별된 패턴
- 자주 나타나는 코드 구조
- 에러 처리 방식
- 의존성 관리 방식
```

#### 2. rule-design-spec.md
```markdown
# 규칙 설계 명세

## 규칙 정의
- 규칙명
- 목적
- 적용 범위
- 조건
- 조치(액션)

## 위반 예시
- Bad 패턴
- Good 패턴

## 적용 레벨
- Critical / Major / Minor
```

#### 3. developer-agent-spec.md
```markdown
# 개발자 에이전트 명세

## 에이전트 정보
- 이름
- 책임
- 입출력

## 필요 스킬
- 스킬 1: [목적]
- 스킬 2: [목적]

## 워크플로우
1. 단계 1
2. 단계 2
3. 단계 3
```

---

## 🔧 사용할 도구/스킬

| 도구 | 목적 |
|------|------|
| **repo-profiler** | GitHub 저장소 구조 분석 |
| **code-analyzer** | 코드 패턴/스타일 분석 |
| **reference-extractor** | C# 패턴 레퍼런스 추출 |
| **grep/glob** | 코드 검색 및 파일 패턴 분석 |
| **WebFetch** | 저장소 README, 문서 조회 |

---

## 📝 워크플로우

```
사용자 요청 (GitHub URL + 분석 대상)
        ↓
[Phase 1] code-analysis-report.md 생성 (Analyzer)
        ↓
[Phase 2] rule-design-spec.md 생성 (Designer)
        ↓
[Phase 3] developer-agent-spec.md 생성 (Spec Generator)
        ↓
[Phase 4] validation-report.md + recommendations.md 생성 (Validator)
        ↓
최종 산출물 검증 완료 및 제공
```

---

## 🎁 출력 디렉토리 구조

```
C:\DevelopRule\agents\rule-developer\
├── AGENT.md                          (이 파일)
├── analyzer/
├── designer/
├── spec-generator/
├── validator/
└── outputs/
    └── {repo-name}/
        ├── code-analysis-report.md       (Phase 1)
        ├── rule-design-spec.md           (Phase 2)
        ├── developer-agent-spec.md       (Phase 3)
        ├── validation-report.md          (Phase 4)
        └── recommendations.md            (Phase 4)
```

---

## 🚀 사용 예시

```
사용자: "https://github.com/owner/repo 분석해서 
         개발자 에이전트 구조 만들어줘"

rule-developer 하네스 (4단계):
1. repo 구조 및 코드 스타일 분석 (Analyzer)
   → code-analysis-report.md
2. 규칙 설계 (Designer)
   → rule-design-spec.md
3. 개발자 에이전트 명세 작성 (Spec Generator)
   → developer-agent-spec.md
4. 규칙 명세 검증 (Validator)
   → validation-report.md
   → recommendations.md

출력:
✓ C:\DevelopRule\agents\rule-developer\outputs\{repo}\code-analysis-report.md
✓ C:\DevelopRule\agents\rule-developer\outputs\{repo}\rule-design-spec.md
✓ C:\DevelopRule\agents\rule-developer\outputs\{repo}\developer-agent-spec.md
✓ C:\DevelopRule\agents\rule-developer\outputs\{repo}\validation-report.md
✓ C:\DevelopRule\agents\rule-developer\outputs\{repo}\recommendations.md
```

---

## 📌 설정

**모드:** acceptEdits (자동 승인)

**권한:** Bash, Read, Glob, Grep, WebFetch, Write 자동 승인

---

**생성:** 2026-04-19  
**업데이트:** 2026-04-21 (Phase 4: Validator 추가)  
**상태:** ✅ 4단계 rule-developer 하네스 완성

## 📊 하네스 구성

| Phase | 에이전트 | 입력 | 출력 |
|-------|----------|------|------|
| 1 | Analyzer | GitHub URL | code-analysis-report.md |
| 2 | Designer | code-analysis-report.md | rule-design-spec.md |
| 3 | Spec Generator | rule-design-spec.md | developer-agent-spec.md |
| 4 | Validator | developer-agent-spec.md | validation-report.md + recommendations.md |
