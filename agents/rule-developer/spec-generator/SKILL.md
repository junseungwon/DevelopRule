# Spec Generator Skill (명세 생성 스킬)

## 📋 스킬 정의

| 속성 | 값 |
|------|-----|
| **스킬명** | generate-agent-spec |
| **역할** | 규칙 설계 기반 에이전트 명세 생성 상세 실행 |
| **입력** | rule-design-spec.md |
| **출력** | developer-agent-spec.md |

---

## 📤 출력: developer-agent-spec.md

```markdown
# 개발자 에이전트 명세서

## 1. 에이전트 개요

| 항목 | 설명 |
|------|------|
| 에이전트명 | developer |
| 목적 | 규칙 기반 코드 검증 및 수정 |
| 입력 | rule-design-spec.md + 대상 코드 |
| 출력 | 검증 보고서, 수정된 코드 |
| 모드 | 자동 수정 / 수동 검토 |

## 2. 에이전트 책임

### 2.1 규칙 검증
- 각 규칙의 위반 사항 탐지
- 위반 위치 및 심각도 기록
- 검증 보고서 생성

### 2.2 자동 수정
- 자동화 가능한 규칙 적용
- 수정된 파일 생성
- 수정 결과 보고

### 2.3 수동 검토 지원
- 수정 불가능한 규칙 안내
- 수정 방법 제시
- 검토 체크리스트 제공

## 3. 필요 스킬

### 3.1 코드 검색 스킬
- grep-rules: 정규표현식 기반 규칙 검색
- pattern-matcher: 패턴 매칭

### 3.2 코드 수정 스킬
- auto-fixer: 자동 수정 규칙 적용
- code-formatter: 코드 포맷팅

### 3.3 보고 스킬
- report-generator: 검증 보고서 생성
- violation-summarizer: 위반 사항 요약

## 4. 워크플로우

### 4.1 초기화
입력: rule-design-spec.md
규칙 파싱 및 로드
규칙 정규표현식 컴파일
대상 파일 스캔

### 4.2 검증
각 파일 순회
  각 규칙 순회
    패턴 매칭 (grep)
    위반 사항 기록
  검증 결과 누적
검증 보고서 생성

### 4.3 수정
자동화 가능 규칙
  각 위반 사항
    자동 수정 적용
    파일 업데이트
수정된 파일 생성
수정 결과 보고

### 4.4 종료
검증 보고서 + 수정된 코드
최종 요약

## 5. 스킬 명세

### 5.1 grep-rules (규칙 검색)
목적: 정규표현식으로 규칙 위반 탐지
입력:
  - rule_id: "R001"
  - pattern: "class\\s+[a-z]"
  - target_files: ["**/*.cs"]
출력:
  - violations: [
      { file: "path/to/file.cs", line: 10, match: "class userController", rule_id: "R001" }
    ]

### 5.2 auto-fixer (자동 수정)
목적: 규칙 기반 자동 수정 적용
입력:
  - rule_id: "R001"
  - violation_list: [...]
  - fix_type: "capitalize_first_letter"
출력:
  - fixed_files: [...]
  - summary: "3 violations fixed"

### 5.3 report-generator (보고서 생성)
목적: 검증 결과를 구조화된 보고서로 생성
입력:
  - violations: [...]
  - fixed_count: 5
  - manual_review_count: 3
출력:
  - report.md: 상세 검증 보고서

## 6. 입출력 명세

### 6.1 입력
rule-design-spec.md:
  - R001: PascalCase 클래스명
  - R002: camelCase 메서드명
  - ...

대상 코드: 특정 디렉토리 또는 파일, 파일 패턴 (*.cs, *.ts)

### 6.2 출력

#### 6.2.1 검증 보고서 (validation-report.md)
# 규칙 검증 보고서

## 요약
- 총 파일: 50
- 총 위반: 25
- 자동 수정 가능: 20
- 수동 검토 필요: 5

## 규칙별 분석

### R001: PascalCase 클래스명
- 위반: 5건
- 수정됨: 5건

### R002: camelCase 메서드명
- 위반: 15건
- 수정됨: 15건

### R020: 계층 분리
- 위반: 5건
- 수정됨: 0건
- 수동 검토 필요

## 파일별 상세 정보

### src/Controllers/UserController.cs
- R001 위반 (Line 5): `class userController`
  → 수정됨: `class UserController`

#### 6.2.2 수정된 코드
fixed-code/
├── src/
│   ├── Controllers/
│   ├── Models/
│   └── Services/

#### 6.2.3 최종 요약 (summary.md)
# 규칙 적용 결과

완료: 20/25 (80%)
수동 검토: 5/25 (20%)

다음 단계:
1. 생성된 validation-report.md 검토
2. 수정된 코드 (fixed-code/) 확인
3. 수동 검토 항목 처리

## 7. 설정 옵션

{
  "auto_fix_enabled": true,
  "fix_priority": "P1,P2,P3",
  "include_patterns": ["**/*.cs", "**/*.ts"],
  "exclude_patterns": ["**/test/**", "**/node_modules/**"],
  "report_format": "markdown",
  "output_dir": "./rule-application-results/"
}

## 8. 에러 처리

| 시나리오 | 처리 방식 |
|---------|----------|
| 규칙 파싱 실패 | 에러 메시지 및 라인 번호 제시 |
| 파일 읽기 실패 | 스킵 및 경고 기록 |
| 자동 수정 실패 | 수동 검토로 표시 |
| 정규표현식 오류 | 유효성 검사 후 재시도 |
```

---

## 🔧 명세 생성 도구

| 도구 | 목적 |
|------|------|
| **Read** | rule-design-spec.md 읽기 |
| **Grep** | 규칙 기반 패턴 검색 |
| **Edit** | 코드 자동 수정 |
| **Write** | 보고서 및 수정 결과 작성 |

---

## ⚙️ 명세 생성 실행 방식

1. 규칙 파싱 및 컴파일
2. 대상 코드 스캔
3. 규칙 적용 및 검증
4. 자동 수정 (가능한 규칙만)
5. 보고서 생성

---

**생성:** 2026-04-21  
**상태:** ✅ 스킬 정의 완료
