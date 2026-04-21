---
name: 성능 프로파일링
description: GC 할당 추적, 핫스팟 최적화, Profiler 활용
category: 규칙
priority: high
---

# UNITY-PROFILE-023: 성능 프로파일링

## 규칙 정의

성능 최적화는 **Unity Profiler 측정 데이터**에 기반해야 한다. `Profiler.BeginSample()` / `EndSample()` 또는 `ProfilerMarker`를 사용해 핫스팟 추적.

## 필요성

1. **데이터 기반 결정**: 추측 대신 측정
2. **재현성**: 성능 회귀 감지
3. **최적화 우선순위**: 실제 병목 확인

## 좋은 예시 ✓

```csharp
using UnityEngine.Profiling;
using Unity.Profiling;

public class AIController : MonoBehaviour
{
    // ✓ ProfilerMarker (재사용, 효율적)
    private static readonly ProfilerMarker _pathfindMarker = 
        new ProfilerMarker("AIController.Pathfind");
    
    void Update()
    {
        using (_pathfindMarker.Auto())
        {
            CalculatePath();
        }
    }
    
    // ✓ 조건부 프로파일링
    [Conditional("ENABLE_PROFILER")]
    private void TrackAllocation()
    {
        Profiler.BeginSample("Heavy Operation");
        // ...
        Profiler.EndSample();
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 프로파일링 없이 추측 최적화
void Update()
{
    // "아마 이게 느릴거야" - 측정 없이 복잡화
    OptimizedButUnmeasured();
}

// ✗ Bad: 매번 string 할당하는 프로파일 샘플
void Update()
{
    Profiler.BeginSample("Frame " + Time.frameCount);  // 매 프레임 할당
    // ...
    Profiler.EndSample();
}
```

## 자동 탐지

```
FOR EACH 성능 크리티컬 메서드
  IF 프로파일 마커 없음
     AND Update/FixedUpdate에서 호출됨
  THEN WARNING (프로파일링 권장)
  
FOR EACH Profiler.BeginSample 호출
  IF 문자열 연결 사용
  THEN VIOLATION (static ProfilerMarker 사용)
```

## 허용 예외

- 명백한 단순 작업 (Transform 할당 등)
- 릴리스 빌드에서 완전 제거된 코드
- 에디터 전용 유틸리티
