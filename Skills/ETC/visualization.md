# 시각화 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | visualization |
| 역할 | Mermaid.js 기반 HTML 시각화 파일 생성 절차 및 형식 기준 정의 |
| 사용 에이전트 | visualizer |
| 참조 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |

---

## 시각화 원칙

1. 모든 레이블은 한국어로 작성 (사용자 명시 요청 시 예외)
2. 이모지 사용 금지 (사용자 명시 요청 시 예외)
3. 단일 HTML 파일 출력 — 외부 JS/CSS 파일로 분리 금지
4. 기본 저장 위치: `{프로젝트루트}/Docs/01_Design/visualizations/`

---

## 다이어그램 유형 선택 기준

| 콘텐츠 유형 | 권장 Mermaid 타입 |
|-----------|-----------------|
| 플로우·트리 구조 | `flowchart TD` / `LR` |
| 클래스 간 호출 흐름 | `sequenceDiagram` |
| 클래스 의존 관계 | `classDiagram` |
| 상태 머신·FSM | `stateDiagram-v2` |
| 일정·마일스톤 | `gantt` |
| 비율·분포 | `pie` |
| 데이터 관계 | `erDiagram` |
| 아이디어 구조 | `mindmap` |

---

## HTML 출력 필수 구성 요소

### head
- `<meta charset="UTF-8">`
- 인라인 CSS (라이트/다크 테마 변수, max-width 1200px)
- Mermaid.js CDN: `https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js`

### body
- 헤더: 제목 + 날짜 + 원본 문서 경로 + 테마 토글 버튼
- 목차: 섹션별 앵커 링크
- 각 섹션: `<h2>` + 설명 + `<div class="mermaid">` 블록
- 푸터: 생성일, 원본 링크

### JavaScript
- localStorage로 테마 저장
- 테마 변경 시 Mermaid 재초기화

---

## 작업 순서

1. 입력 문서 Read로 전체 내용 파악
2. 각 섹션을 적절한 다이어그램 타입에 매핑
3. Mermaid 코드 블록 작성 (한국어 레이블)
4. HTML 템플릿에 삽입
5. 지정 경로에 Write
6. 완료 후 경로 및 브라우저 열람 방법 안내

---

## 품질 기준

- 다이어그램당 노드 수: 20개 이하 권장, 50개 이상이면 분할
- 긴 레이블: `<br/>` 또는 줄바꿈 처리
- 색상: 의미 구분용만 (성공/실패/진행 등)
- 화살표 방향: 데이터 흐름과 반드시 일치

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 원본 문서 불명확 | 변환 대상 명확화 요청 |
| Mermaid 문법 오류 | 생성 후 문법 검증, 오류 시 수정 재작성 |
| 노드 50개 이상 | 섹션별 분할 제안 |
| 오프라인 배포 필요 | Mermaid.js 로컬 포함 방법 안내 |

---

생성: 2026-05-01
