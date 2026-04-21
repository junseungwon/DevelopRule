---
name: 마커스 기반 설계
description: Tag/Layer 올바른 사용, 마스크 활용, 성능
category: 규칙
priority: high
---

# UNITY-MARKER-035: 마커 기반 설계

## 규칙 정의

**Tag**는 문자열 비교이므로 한정적 사용. **Layer**는 물리/렌더링 필터링에 사용. **CompareTag()** 사용(직접 tag 비교 대비 빠름). LayerMask는 비트 연산.

## 필요성

1. **성능**: CompareTag가 tag == 비교보다 빠름
2. **안전성**: Tag는 문자열 오타 가능
3. **효율**: Layer 마스크로 일괄 필터링

## 좋은 예시 ✓

```csharp
public class Collector : MonoBehaviour
{
    [SerializeField] private LayerMask _pickupLayer;
    
    // ✓ CompareTag 사용
    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Item"))
        {
            Collect(other.gameObject);
        }
    }
    
    // ✓ LayerMask로 물리 필터링
    void ScanArea()
    {
        var hits = Physics.OverlapSphere(
            transform.position, 5f, _pickupLayer);  // 효율적 필터
    }
    
    // ✓ Layer 비트 체크
    bool IsPickup(GameObject obj)
    {
        return (_pickupLayer.value & (1 << obj.layer)) != 0;
    }
}

// ✓ Tag/Layer 상수화
public static class Tags
{
    public const string Player = "Player";
    public const string Enemy = "Enemy";
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 직접 tag 비교 (느림)
void OnTriggerEnter(Collider other)
{
    if (other.tag == "Item") { /* ... */ }  // 문자열 할당 + 비교
}

// ✗ Bad: 문자열 하드코딩 (오타 취약)
void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Iten"))  // 오타 - 조용히 실패
    {
        Collect(other.gameObject);
    }
}

// ✗ Bad: Layer 대신 Tag로 필터링
void ScanArea()
{
    var all = Physics.OverlapSphere(transform.position, 5f);
    foreach (var hit in all)
    {
        if (hit.CompareTag("Item")) { /* ... */ }  // 모든 충돌 체크
    }
}
```

## 자동 탐지

```
FOR EACH "obj.tag == string" 비교
  THEN VIOLATION (CompareTag 권장)
  
FOR EACH Physics.OverlapXX / Raycast
  IF LayerMask 매개변수 없음
     AND 특정 레이어만 필요
  THEN WARNING
  
FOR EACH Tag/Layer 문자열 리터럴 반복
  THEN WARNING (상수화)
```

## 허용 예외

- 테스트 코드 내 직접 비교
- 한 번만 사용되는 Tag
