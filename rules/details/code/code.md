# 코드 작성 규칙 허브

> 대상: Unity C# 개발 | 목적: 일관성 있는 코드 작성

## 핵심 규칙 (항상 적용)

- `[SerializeField]` → `[Tooltip]` 필수
- `public` 메서드·클래스·프로퍼티·변수 → `/// <summary>` 필수
- `private` 메서드도 `/// <summary>` 필수
- 매직 넘버 금지 → 상수 또는 `[SerializeField]` 추출
- `GetComponent` → `Awake`에서만 호출
- 인터페이스 → `I` 접두사 필수 (`IDamageable`, `IInteractable`)
- Enum → `E` 접두사 필수 (`EGameState`, `EWeaponType`)
- 현재 코드베이스에서 참조 0(`rg` 기준)인 코드/구조 작성 금지

## 운영 원칙

- 처음부터 완벽하게 만들지 않는다
- AI가 규칙을 어긴 경우 → 해당 서브 파일 금지 패턴 목록에 한 줄 추가

## 세부 규칙 참조

| 작업 상황 | 참조 파일 |
|---|---|
| 코드 포맷·스타일 | `C:\DevelopRule\rules\details\code\code-style.md` |
| 클래스 내부 구조 | `C:\DevelopRule\rules\details\code\code-class.md` |
| 코드 패턴·안티패턴 | `C:\DevelopRule\rules\details\code\code-pattern.md` |
