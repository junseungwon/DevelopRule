# 주석 작성 규칙 허브

## 핵심 규칙 (항상 적용)

- `[SerializeField]` 변수 → `[Tooltip]` 필수
- `public` 메서드·클래스·프로퍼티·변수 → `/// <summary>` 필수
- 주석은 Why 위주, What 설명 금지
- `//` 뒤 스페이스 한 칸 필수
- 장식용 주석 금지 (`//-----`, `//#####` 등)
- 메서드 내부 `#region` 사용 금지

## 운영 원칙

- 처음부터 완벽하게 만들지 않는다
- AI가 규칙을 어긴 경우 → 해당 서브 파일 금지 패턴 목록에 한 줄 추가

## 세부 규칙 참조

| 작업 상황 | 참조 파일 |
|---|---|
| `#region` 작성 시 | `C:\DevelopRule\rules\details\comment\comment-region.md` |
| 메서드 주석 작성 시 | `C:\DevelopRule\rules\details\comment\comment-function.md` |
| 변수 주석 작성 시 | `C:\DevelopRule\rules\details\comment\comment-variable.md` |
