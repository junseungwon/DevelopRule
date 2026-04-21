---
name: 메모리 최적화
description: 박싱/언박싱 회피, 임시 객체 생성 최소화, GC 할당 추적
category: 규칙
priority: high
---

# UNITY-MEMORY-014: 메모리 최적화

## 규칙 정의

핫패스에서 **박싱/언박싱 회피**, 임시 객체 생성 최소화, GC 할당 추적. StringBuilder 사용, 배열 재사용, struct 활용.

## 필요성

1. **GC 스파이크 방지**: 프레임 드롭 방지
2. **모바일 성능**: 제한된 메모리 효율 사용
3. **프로파일링 용이**: 할당 소스 명확

## 좋은 예시 ✓

```csharp
// ✓ StringBuilder 사용
private readonly StringBuilder _sb = new();
public string BuildMessage()
{
    _sb.Clear();
    _sb.Append("Player: ");
    _sb.Append(_name);
    return _sb.ToString();
}

// ✓ 리스트 재사용 (Clear로 용량 유지)
private List<Enemy> _tempList = new(64);
void FindNearbyEnemies()
{
    _tempList.Clear();
    foreach (var enemy in _allEnemies)
    {
        if (IsNear(enemy)) _tempList.Add(enemy);
    }
}

// ✓ struct로 박싱 회피
void Log<T>(T value) where T : struct
{
    Debug.Log(value.ToString());  // 박싱 없음
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 문자열 연결
void Update()
{
    string msg = "Player " + playerName + " at " + position;  // 매 프레임 할당
}

// ✗ Bad: LINQ / foreach로 IEnumerable 박싱
void Update()
{
    foreach (var num in new int[] { 1, 2, 3 })  // 매 프레임 배열 할당
    {
        // ...
    }
}

// ✗ Bad: 박싱 발생
object boxed = 42;  // int가 object로 박싱
int unboxed = (int)boxed;  // 언박싱
```

## 자동 탐지

```
FOR EACH "new" 키워드
  IF 속한 메서드가 Update/FixedUpdate/LateUpdate
  THEN VIOLATION (할당 검토)
  
FOR EACH 문자열 "+" 연산자
  IF 핫패스 내부
  THEN WARNING (StringBuilder 권장)
  
FOR EACH value type → object 캐스팅
  THEN VIOLATION (박싱)
```

## 허용 예외

- 객체 풀에서 반환된 인스턴스
- 컴파일러 최적화되는 상수 문자열
- 명시적 할당이 필요한 이벤트 데이터
