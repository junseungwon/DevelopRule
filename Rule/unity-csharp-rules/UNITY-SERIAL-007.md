---
name: 직렬화 속성 적용
description: 에디터에서 노출하려는 필드에는 적절한 직렬화 속성을 명시
category: 규칙
priority: high
---

# UNITY-SERIAL-007: 직렬화 속성 적용

## 규칙 정의

에디터 인스펙터에 노출하려는 private 필드에는 **[SerializeField]** 속성을 명시한다. Public 필드 사용을 지양하고 직렬화 의도를 명확화한다.

```csharp
[SerializeField] private float _speed;   // ✓ 명시적
public float speed;                      // ✗ 캡슐화 위반
```

## 필요성

1. **캡슐화 유지**: 외부 접근은 프로퍼티, 직렬화는 속성으로 분리
2. **의도 명확화**: 에디터 노출 의도 명시
3. **리팩토링 안전**: 필드명 변경 시 [FormerlySerializedAs] 지원

## 좋은 예시 ✓

```csharp
public class PlayerController : MonoBehaviour
{
    [SerializeField] private float _moveSpeed = 5f;
    [SerializeField] private GameObject _bulletPrefab;
    [SerializeField, Range(0, 100)] private int _health = 100;
    
    [HideInInspector] public bool isInvincible;  // 런타임 전용
}

public class WeaponData : ScriptableObject
{
    [SerializeField] private string _weaponName;
    [SerializeField, TextArea] private string _description;
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Public 필드로 직렬화
public class PlayerController : MonoBehaviour
{
    public float moveSpeed = 5f;       // 캡슐화 파괴
    public GameObject bulletPrefab;    // 외부 직접 수정 가능
}

// ✗ Bad: Private 필드에 [SerializeField] 없음 (직렬화 안됨)
public class Enemy : MonoBehaviour
{
    private int _health = 100;  // 에디터에 노출되지 않음
}
```

## 자동 탐지

```
FOR EACH field in MonoBehaviour/ScriptableObject
  IF field is public
     AND NOT const/readonly
  THEN VIOLATION (SerializeField 권장)
  
  IF field is private
     AND needs editor exposure
     AND NOT has [SerializeField]
  THEN WARNING
```

## 예외

- const/readonly 필드: public 허용
- 인스펙터 비노출 필드: [HideInInspector] 사용
- 데이터 전송 객체(DTO): 직렬화 불필요 시 속성 생략
