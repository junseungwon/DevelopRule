# 함수 주석 규칙

## 적용 대상
| 대상 | 작성 여부 |
|---|---|
| public 클래스·메서드·프로퍼티 | 필수 |
| private·protected 메서드 | 복잡한 로직일 때만 선택 |

## 태그 규칙
- `<summary>` : 항상 필수
- `<param>`   : 매개변수 있을 시 필수
- `<returns>` : 반환값 있을 시 필수
- `<remarks>` : 추가 설명 필요 시 선택

## 작성 예시
```csharp
/// <summary>데미지를 가하고 체력이 0 이하면 사망 처리합니다.</summary>
/// <param name="amount">적용할 데미지 수치 (양수)</param>
/// <returns>남은 현재 체력</returns>
public int ApplyDamage(int amount) { }
```

## 인라인 주석
- Why 위주 작성, 코드 바로 윗줄에 작성, // 뒤 스페이스 한 칸

```csharp
// 기획 요청(26.04.01): 독 데미지는 최소 체력 1을 남겨야 함
if (health - poisonDamage <= 0) health = 1;
```

## 금지 패턴 목록 (실수 발생 시 누적 추가)
- public 메서드·클래스에 <summary> 누락 → 금지
- What을 설명하는 인라인 주석 → 금지
- 주석을 코드 우측 끝에 작성 → 금지
