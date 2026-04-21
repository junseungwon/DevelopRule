# Rule Developer Orchestrator (규칙 개발자 조정자)

## 🏗️ 시스템 아키텍처

```
User (GitHub URL)
    ↓
[Analyzer Agent]
    ↓
code-analysis-report.md
    ↓
[Designer Agent]
    ↓
rule-design-spec.md
    ↓
[Spec Generator Agent]
    ↓
developer-agent-spec.md
    ↓
[Validator Agent]
    ↓
validation-report.md + recommendations.md
    ↓
Output (최종 에이전트 명세 + 검증 완료)
```

---

## 🤖 에이전트 소개

### Phase 1: Analyzer (분석자)
**위치**: `C:\DevelopRule\agents\rule-developer\analyzer\AGENT.md`

```
책임: GitHub 저장소 분석
입력: GitHub URL
출력: code-analysis-report.md
```

**주요 작업**:
- 저장소 구조 파악
- 코드 스타일 규칙 추출
- 설계 패턴 식별
- 계층 구조 분석

---

### Phase 2: Designer (설계자)
**위치**: `C:\DevelopRule\agents\rule-developer\designer\AGENT.md`

```
책임: 코드 분석 결과 기반 규칙 설계
입력: code-analysis-report.md
출력: rule-design-spec.md
```

**주요 작업**:
- 분석 보고서 검토
- 규칙 정의 작성
- 위반/준수 예시 제공
- 자동화 가능 여부 판정
- 규칙 우선순위 결정

---

### Phase 3: Spec Generator (명세 생성자)
**위치**: `C:\DevelopRule\agents\rule-developer\spec-generator\AGENT.md`

```
책임: 규칙 설계 기반 개발자 에이전트 명세 생성
입력: rule-design-spec.md
출력: developer-agent-spec.md
```

**주요 작업**:
- 규칙 설계 검토
- 에이전트 요구사항 정의
- 필요 스킬 명세
- 워크플로우 설계
- 입출력 명세

---

### Phase 4: Validator (규칙 점검자)
**위치**: `C:\DevelopRule\agents\rule-developer\validator\AGENT.md`

```
책임: 규칙 명세의 품질 및 실행 가능성 검증
입력: developer-agent-spec.md
출력: validation-report.md + recommendations.md
```

**주요 작업**:
- 규칙 명세의 명확성 검증
- 규칙의 실행 가능성 검증
- 에이전트-스킬-워크플로우 정합성 검증
- 품질 리포트 작성
- 개선 권고사항 제시

---

## 📊 데이터 플로우

```
┌─────────────────────────────────────────────────────────┐
│                    GitHub Repository                     │
│              https://github.com/owner/repo              │
└──────────────────────────┬────────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │   Analyzer  │
                    └──────┬──────┘
                           │
          ┌────────────────▼────────────────┐
          │  code-analysis-report.md        │
          │  ├── 저장소 정보                 │
          │  ├── 디렉토리 구조               │
          │  ├── 코드 스타일 규칙            │
          │  ├── 설계 패턴                   │
          │  └── 계층 구조                   │
          └────────────────┬────────────────┘
                           │
                    ┌──────▼──────┐
                    │  Designer   │
                    └──────┬──────┘
                           │
          ┌────────────────▼────────────────┐
          │  rule-design-spec.md            │
          │  ├── 규칙 목록 (R001-Rxxx)       │
          │  ├── 규칙 상세 명세              │
          │  ├── 위반/준수 예시              │
          │  ├── 조치 (Action) 정의          │
          │  ├── 규칙 그룹화                 │
          │  └── 시행 전략                   │
          └────────────────┬────────────────┘
                           │
                  ┌────────▼──────────┐
                  │ Spec Generator    │
                  └────────┬──────────┘
                           │
              ┌────────────▼─────────────┐
              │  developer-agent-spec.md │
              │  ├── 정의                │
              │  ├── 책임                │
              │  ├── 스킬                │
              │  ├── 워크플로우          │
              │  └── I/O                 │
              └────────────┬─────────────┘
                           │
                  ┌────────▼──────────┐
                  │   Validator       │
                  └────────┬──────────┘
                           │
   ┌───────────────────────┼───────────────────────┐
   │                       │                       │
┌──▼─────────────┐  ┌──────▼────────┐  ┌─────────▼──────┐
│ validation-    │  │recommendations│  │ implementation │
│ report.md      │  │ .md            │  │ guide.md       │
│                │  │                │  │                │
│ ├── 검증 요약  │  │ ├── 우선순위   │  │ ├── 시작 가이드 │
│ ├── 명확성     │  │ │  개선 항목   │  │ ├── 워크플로우  │
│ ├── 실행 가능  │  │ ├── 규칙별     │  │ ├── 예제        │
│ ├── 정합성     │  │ │  개선안      │  │ └── FAQ         │
│ └── 다음 단계  │  │ └── 확장성     │  │                │
└────────────────┘  └────────────────┘  └────────────────┘
```

---

## 🔄 순차 실행 흐름

### Step 1: Analyzer 실행
```bash
입력:
  - GitHub URL: https://github.com/owner/repo

처리:
  1. 저장소 메타데이터 추출
  2. 디렉토리 구조 분석
  3. 코드 스타일 규칙 추출
  4. 설계 패턴 식별

출력:
  - code-analysis-report.md
  - 분석 완료 신호
```

### Step 2: Designer 실행
```bash
입력:
  - code-analysis-report.md (Step 1 출력)

처리:
  1. 분석 보고서 검토
  2. 발견사항별 규칙 설계
  3. 규칙 정의서 작성
  4. 자동화 가능 여부 판정

출력:
  - rule-design-spec.md
  - 설계 완료 신호
```

### Step 3: Spec Generator 실행
```bash
입력:
  - rule-design-spec.md (Step 2 출력)

처리:
  1. 규칙 설계 검토
  2. 에이전트 요구사항 정의
  3. 명세서 작성
  4. 워크플로우 설계

출력:
  - developer-agent-spec.md
  - 명세 생성 완료 신호
```

### Step 4: Validator 실행
```bash
입력:
  - developer-agent-spec.md (Step 3 출력)

처리:
  1. 규칙 명세 분석
  2. 명확성 검증 (규칙 정의의 명확함 정도)
  3. 실행 가능성 검증 (자동화 가능 여부)
  4. 정합성 검증 (에이전트-스킬-워크플로우 대응)
  5. 문제점 분류 (Critical, Major, Minor)
  6. 개선 권고사항 작성

출력:
  - validation-report.md
  - recommendations.md
  - 검증 완료 신호 (개발 진행 or 규칙 개선 결정)
```

---

## 📁 디렉토리 구조

```
C:\DevelopRule\agents\rule-developer\
├── ORCHESTRATOR.md                    (이 파일)
├── AGENT.md                           (구버전 - 폐기 예정)
├── analyzer/
│   └── AGENT.md                       (Phase 1)
├── designer/
│   └── AGENT.md                       (Phase 2)
├── spec-generator/
│   └── AGENT.md                       (Phase 3)
├── validator/
│   └── AGENT.md                       (Phase 4)
└── outputs/
    └── {repo-name}/
        ├── code-analysis-report.md    (Analyzer 출력)
        ├── rule-design-spec.md        (Designer 출력)
        ├── developer-agent-spec.md    (Spec Generator 출력)
        ├── validation-report.md       (Validator 출력)
        └── recommendations.md         (Validator 출력)
```

---

## 🚀 사용 예시

### 한 번에 실행하기

```
사용자: "이 GitHub 저장소를 분석해서 개발자 에이전트 만들어줘"
입력: https://github.com/owner/repo

실행:
  1. Analyzer → code-analysis-report.md 생성
  2. Designer → rule-design-spec.md 생성
  3. Spec Generator → developer-agent-spec.md 생성
  4. Validator → validation-report.md + recommendations.md 생성

출력:
  ✅ code-analysis-report.md
  ✅ rule-design-spec.md
  ✅ developer-agent-spec.md
  ✅ validation-report.md
  ✅ recommendations.md (규칙 개선 권고사항)
```

### 각 Phase별 단독 실행하기

```
# Phase 1만 실행
사용자: "이 GitHub 저장소만 분석해줘"

# Phase 2만 실행
사용자: "이 분석 보고서를 바탕으로 규칙을 설계해줘"

# Phase 3만 실행
사용자: "이 규칙 설계를 바탕으로 에이전트 명세를 생성해줘"

# Phase 4만 실행
사용자: "이 에이전트 명세를 검증해줘"
```

---

## ✅ 검증 체크리스트

### Analyzer 완료 기준
- [ ] 저장소 메타데이터 추출 완료
- [ ] 디렉토리 구조 분석 완료
- [ ] 코드 스타일 규칙 추출 완료
- [ ] 설계 패턴 식별 완료
- [ ] code-analysis-report.md 생성됨

### Designer 완료 기준
- [ ] 분석 보고서 검토 완료
- [ ] 규칙 목록 작성 완료
- [ ] 각 규칙 상세 명세 작성 완료
- [ ] 위반/준수 예시 제공됨
- [ ] rule-design-spec.md 생성됨

### Spec Generator 완료 기준
- [ ] 규칙 설계 검토 완료
- [ ] 에이전트 요구사항 정의됨
- [ ] 필요 스킬 명세 작성됨
- [ ] 워크플로우 설계됨
- [ ] developer-agent-spec.md 생성됨

### Validator 완료 기준
- [ ] 규칙 명세 명확성 검증 완료
- [ ] 실행 가능성 검증 완료
- [ ] 에이전트-스킬-워크플로우 정합성 검증 완료
- [ ] 문제점 분류 완료 (Critical, Major, Minor)
- [ ] validation-report.md 생성됨
- [ ] recommendations.md 생성됨 (개선 권고사항)
- [ ] 다음 단계 결정됨 (개발 진행 or 규칙 개선)

---

## 🎯 최종 산출물

| 파일 | 생성자 | 목적 |
|------|--------|------|
| code-analysis-report.md | Analyzer | GitHub 저장소 분석 결과 |
| rule-design-spec.md | Designer | 규칙 설계 명세 |
| developer-agent-spec.md | Spec Generator | 개발자 에이전트 명세 |
| validation-report.md | Validator | 규칙 명세 검증 보고서 |
| recommendations.md | Validator | 규칙 개선 권고사항 |

---

## 📌 주의사항

1. **순차 실행**: 각 Phase는 순서대로 실행되어야 함
2. **입력 의존성**: 이전 Phase의 출력이 다음 Phase의 입력
3. **에러 처리**: 각 Phase에서 오류 발생 시 중단 및 상세 리포트
4. **재실행**: 특정 Phase만 재실행 가능 (입력 파일 제공 시)

---

**생성:** 2026-04-19  
**업데이트:** 2026-04-21 (Phase 4: Validator 추가)  
**상태:** ✅ 4단계 오케스트레이션 완료

**다음 단계**: GitHub URL을 제공받아 Analyzer부터 Validator까지 순차 실행
