---
name: GameObject/Component 관리
description: GetComponent 캐싱, 안전한 접근, 생명주기 관리
category: 규칙
priority: high
---

# UNITY-GAMEOBJ-013: GameObject/Component 관리

## 규칙 정의

`GetComponent<T>()`는 **Awake/Start에서 캐싱**하고, Update 등 자주 호출되는 경로에서는 캐시된 참조 사용. Component 생명주기는 OnEnable/OnDisable로 관리.

## 필요성

1. **성능**: GetComponent는 비용 높음 (리플렉션 기반)
2. **안전성**: Destroy된 객체 접근 방지
3. **생명주기 명확화**: 활성화/비활성화 상태 추적

## 좋은 예시 ✓

```csharp
public class PlayerController : MonoBehaviour
{
    private Rigidbody _rigidbody;
    private Animator _animator;
    
    // ✓ Awake에서 캐싱
    void Awake()
    {
        _rigidbody = GetComponent<Rigidbody>();
        _animator = GetComponent<Animator>();
    }
    
    void Update()
    {
        // ✓ 캐시된 참조 사용
        _rigidbody.AddForce(Vector3.forward);
    }
    
    // ✓ TryGetComponent로 안전 접근
    public void TryJump()
    {
        if (TryGetComponent<Jumper>(out var jumper))
        {
            jumper.Jump();
        }
    }
}
```

## 나쁜 예시 ✗

```csharp
public class PlayerController : MonoBehaviour
{
    // ✗ Bad: 매 프레임 GetComponent
    void Update()
    {
        GetComponent<Rigidbody>().AddForce(Vector3.forward);
    }
    
    // ✗ Bad: Null 체크 없이 접근
    void OnTriggerEnter(Collider other)
    {
        other.GetComponent<Enemy>().TakeDamage(10);  // null 가능
    }
}
```

## 자동 탐지

```
FOR EACH GetComponent 호출
  IF 속한 메서드가 Update/FixedUpdate/LateUpdate
  THEN VIOLATION (캐싱 필요)
  
  IF GetComponent 결과 즉시 사용
     AND null 체크 없음
  THEN WARNING
```

## 허용 예외

- 한 번 호출되는 초기화 메서드
- 에디터 전용 코드 (#if UNITY_EDITOR)
- TryGetComponent 사용 시 (null 체크 내장)
