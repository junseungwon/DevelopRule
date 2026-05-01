# 파일 생성·구조 관리 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | file-management |
| 역할 | 파일 및 디렉토리 생성·이동·삭제·구조 문서 최신화 절차 정의 |
| 사용 에이전트 | file-creator, file-structure-manager |
| 참조 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |

---

## 파일 생성 원칙 (file-creator)

1. 생성 전 반드시 대상 디렉토리 존재 여부 확인
2. 기존 파일 덮어쓰기 전 사용자 확인 (자동 승인 모드 제외)
3. 파일명·경로는 요청된 컨벤션을 그대로 따름
4. 생성 범위를 벗어난 추가 파일 생성 금지

### Unity 파일 생성 위치 기준

| 파일 유형 | 기본 경로 |
|---------|---------|
| C# 스크립트 | `Assets/02_Scripts/` 하위 도메인별 |
| ScriptableObject | `Assets/88_ScriptableObjects/` |
| 애니메이터 | `Assets/08_Animator/` |
| 에셋 (머테리얼 등) | `Assets/04_Materials/` 등 |
| 문서 | `docs/` 하위 |

---

## 파일 구조 관리 원칙 (file-structure-manager)

1. 파일 변경 발생 시 즉시 구조 문서 업데이트 (사후 처리 금지)
2. 구조 문서는 항상 실제 파일 시스템과 일치하도록 유지
3. 파일 탐색 시 구조 문서를 먼저 참조 후 실제 탐색
4. 업데이트 후 documenter에게 문서화 요청

구조 문서 위치: `d:\ProjectFiles\JellyMolly\docs\file-structure.md`

---

## 구조 문서 형식

```markdown
# 프로젝트 파일 구조
> 마지막 업데이트: YYYY-MM-DD

## Assets/02_Scripts/
├── Character/
│   ├── Jelly/
│   │   ├── JellyPlayer.cs        — 젤리 플레이어 메인 컨트롤러 [NEW]
│   │   └── ...
│   └── ...
└── ...
```

- 각 파일 옆에 한 줄 설명 필수
- 폴더 단위로 역할 설명 포함
- 신규 추가 항목은 `[NEW]` 태그 표시

---

## Git Push 절차 (git-push)

### 커밋 메시지 형식

```
#<type>: <title>
```

타입 목록:
| 타입 | 사용 시점 |
|------|---------|
| `#feat` | 신규 기능 추가 |
| `#fix` | 버그 수정 |
| `#asset` | 에셋 추가·수정 |
| `#scene` | 씬 파일 변경 |
| `#refactor` | 동작 변경 없는 코드 정리 |
| `#perf` | 성능 개선 |
| `#docs` | 문서 변경 |
| `#chore` | 빌드·패키지 변경 |
| `#revert` | 이전 커밋 롤백 |

### 커밋 전 안전 확인

- main/master 브랜치 직접 push 금지 (사용자 명시 승인 없이)
- secrets·credential 파일 스테이징 금지
- 의도치 않은 untracked 파일 포함 여부 경고

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 경로 권한 오류 | 사용자에게 대안 경로 제안 |
| 파일 충돌 | 덮어쓰기 여부 확인 후 진행 |
| 구조 문서 불일치 | 실제 파일 시스템 기준으로 재동기화 |
| 파일 위치 불명확 | Glob/Grep으로 탐색 후 구조 문서에 반영 |

---

생성: 2026-05-01
