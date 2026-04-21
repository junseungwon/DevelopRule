---
name: 로깅 규칙
description: Debug.Log 레벨별 사용, 프로덕션 코드 정리, 포맷팅
category: 규칙
priority: high
---

# UNITY-LOG-012: 로깅 규칙

## 규칙 정의

로그 레벨을 상황에 맞게 사용: `Debug.Log`(정보), `Debug.LogWarning`(주의), `Debug.LogError`(오류). 프로덕션에서는 `[Conditional("UNITY_EDITOR")]` 또는 컴파일 지시문으로 제거.

## 필요성

1. **성능**: 프로덕션에서 문자열 할당 방지
2. **디버깅 효율**: 레벨별 필터링 가능
3. **로그 노이즈**: 중요도별 분리

## 좋은 예시 ✓

```csharp
// ✓ 레벨별 적절 사용
public void LoadLevel(int index)
{
    Debug.Log($"Loading level {index}");  // 정보
    
    if (index < 0)
    {
        Debug.LogError($"Invalid level index: {index}");
        return;
    }
    
    if (index > _maxLevel)
    {
        Debug.LogWarning($"Level {index} exceeds max, clamping");
        index = _maxLevel;
    }
}

// ✓ 조건부 로깅 (프로덕션 제거)
[Conditional("UNITY_EDITOR"), Conditional("DEBUG_LOG")]
private void LogDebug(string message)
{
    Debug.Log($"[Debug] {message}");
}

// ✓ context 객체 전달
Debug.Log("Player hit", this.gameObject);
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 모든 것을 Log로
public void LoadLevel(int index)
{
    Debug.Log($"Loading level {index}");
    if (index < 0) Debug.Log($"Error: invalid {index}");  // Error여야 함
}

// ✗ Bad: 프로덕션 남은 디버그 로그
void Update()
{
    Debug.Log("Position: " + transform.position);  // 매 프레임 GC 압력
}

// ✗ Bad: 문자열 연결 (LogError는 항상 실행)
Debug.LogError("User " + userId + " failed: " + errorCode);
// ✓ Better: string interpolation 또는 조건부
```

## 자동 탐지

```
FOR EACH Debug.Log 호출
  IF 속한 메서드가 Update/FixedUpdate
     AND NOT [Conditional] 적용
  THEN WARNING (성능 이슈)
  
  IF "error" / "fail" 키워드 포함
     AND Debug.Log 사용 (LogError 아님)
  THEN VIOLATION
```

## 예외

- 이벤트 기반 로깅: 사용자 액션 응답
- 개발용 로깅: [Conditional] 적용 시
