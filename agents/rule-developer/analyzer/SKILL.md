# Analyzer Skill (분석 스킬)

## 📋 스킬 정의

| 속성 | 값 |
|------|-----|
| **스킬명** | analyze-code |
| **역할** | GitHub 저장소 코드 구조 및 스타일 분석 상세 실행 |
| **입력** | GitHub URL |
| **출력** | code-analysis-report.md |

---

## 📤 출력: code-analysis-report.md

```markdown
# 코드 분석 보고서

## 1. 저장소 정보
- **언어**: C# / TypeScript / Python
- **프레임워크**: Unity / React / FastAPI
- **규모**: 파일 수, LOC

## 2. 디렉토리 구조
```
src/
├── Controllers/
├── Models/
├── Services/
├── Utils/
├── Views/
```

## 3. 코드 스타일

### 3.1 네이밍 컨벤션
- **클래스**: PascalCase (예: UserController)
- **메서드**: camelCase (예: getUserById)
- **상수**: UPPER_SNAKE_CASE (예: MAX_SIZE)
- **private 필드**: _camelCase (예: _userId)

### 3.2 들여쓰기
- 탭/스페이스: [4 spaces / tabs]
- 라인 길이: [80 / 100 / 120]

### 3.3 괄호 스타일
- 함수 선언
- 조건문 형식
- 클래스 정의

### 3.4 주석 스타일
- 단일 라인: //
- 다중 라인: /* */
- 문서 주석: /** */

## 4. 설계 패턴 및 규칙 준수도

### 4.1 구조적 패턴
- Singleton 패턴 사용 여부
- Factory/Builder 패턴
- Observer/Event 패턴

### 4.2 에러 처리
- Try-Catch 방식
- 커스텀 예외 정의
- 로깅 규칙
- null 안전성

### 4.3 의존성 관리
- DI 컨테이너 사용 여부
- Manual injection
- 정적 참조 패턴

## 5. 계층 구조
- **Models**: 데이터 구조
- **Services**: 비즈니스 로직
- **Controllers/Views**: UI/API
- **Utils**: 유틸리티

## 6. 주요 발견사항
- 일관된 패턴 (강점)
- 불일치 영역 (개선점)
- 도메인 특화 규칙
```

---

## 🔧 분석 도구

| 도구 | 목적 |
|------|------|
| **repo-profiler** | 저장소 구조 및 기술 스택 분석 |
| **code-analyzer** | 코드 패턴/스타일 식별 |
| **grep/glob** | 파일 패턴 검색 |
| **WebFetch** | README, 문서 조회 |

---

## ⚙️ 분석 실행 방식

1. 저장소 구조 분석 (repo-profiler)
2. 코드 샘플 추출 및 스타일 분석 (code-analyzer)
3. 패턴 및 컨벤션 식별 (grep)
4. 보고서 생성

---

**생성:** 2026-04-21  
**상태:** ✅ 스킬 정의 완료
