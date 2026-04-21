# Designer Skill (설계 스킬)

## 📋 스킬 정의

| 속성 | 값 |
|------|-----|
| **스킬명** | design-rules |
| **역할** | 분석 결과 기반 규칙 설계 상세 실행 |
| **입력** | code-analysis-report.md |
| **출력** | rule-design-spec.md |

---

## 📤 출력: rule-design-spec.md

```markdown
# 규칙 설계 명세

## 1. 규칙 목록

| ID | 규칙명 | 적용 범위 | 심각도 |
|----|--------|----------|--------|
| R001 | PascalCase 클래스명 | 모든 클래스 | Major |
| R002 | camelCase 메서드명 | 모든 메서드 | Major |
| R003 | 4-space 들여쓰기 | 모든 파일 | Minor |

## 2. 규칙 상세 명세

### R001: PascalCase 클래스명

설명: 모든 클래스 이름은 PascalCase를 따라야 함

적용 범위:
- 대상: *.cs, *.ts (확장자)
- 제외: 인터페이스는 별도 규칙
- 적용 계층: 모든 계층

조건: class\s+[a-z]  (소문자로 시작)

위반 예시 (Bad):
class userController { }
class productService { }

준수 예시 (Good):
class UserController { }
class ProductService { }

조치: Major, "클래스명은 PascalCase를 사용하세요", 자동 수정 가능

예외: 테스트 클래스는 Test 접미사 필수, Mock 객체는 Mock 접미사 필수

### R002: camelCase 메서드명

설명: 모든 메서드 이름은 camelCase를 따라야 함

적용 범위:
- 대상: public/internal 메서드
- 제외: private 메서드는 _camelCase
- 적용 계층: 모든 계층

조건: (public|internal)\s+(void|string|int|...)\s+[A-Z]

위반 예시 (Bad):
public void GetUser() { }
public string SetValue() { }

준수 예시 (Good):
public void getUser() { }
public string setValue() { }

조치: Major, "메서드명은 camelCase를 사용하세요"

## 3. 규칙 그룹

### 3.1 네이밍 규칙
- R001: 클래스명
- R002: 메서드명
- R003: 변수명
- R004: 상수명

### 3.2 코드 스타일 규칙
- R010: 들여쓰기
- R011: 라인 길이
- R012: 공백 규칙

### 3.3 구조 규칙
- R020: 계층 분리
- R021: 의존성 방향
- R022: 에러 처리

## 4. 규칙 적용 전략

| 우선순위 | 규칙 유형 | 시행 단계 |
|----------|----------|----------|
| P1 | 네이밍 규칙 | Phase 1 (즉시) |
| P2 | 구조 규칙 | Phase 2 (1개월) |
| P3 | 스타일 규칙 | Phase 3 (3개월) |

## 5. 자동화 지원

### 5.1 자동 수정 가능 규칙
- R001: PascalCase 클래스명
- R002: camelCase 메서드명
- R010: 들여쓰기

### 5.2 수동 검토 필요 규칙
- R020: 계층 분리
- R021: 의존성 방향
- R022: 에러 처리
```

---

## 🔧 설계 도구

| 도구 | 목적 |
|------|------|
| **Read** | code-analysis-report.md 읽기 |
| **Write** | rule-design-spec.md 작성 |

---

## ⚙️ 설계 실행 방식

1. 분석 보고서 상세 검토
2. 발견사항별 규칙 설계
3. 규칙 정의서 작성
4. 예시 및 조치 정리
5. 규칙 목록 최종화

---

**생성:** 2026-04-21  
**상태:** ✅ 스킬 정의 완료
