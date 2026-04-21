---
name: 오브젝트 풀링
description: 풀 구현, 초기화 패턴, 메모리 관리
category: 규칙
priority: high
---

# UNITY-POOL-034: 오브젝트 풀링

## 규칙 정의

빈번한 `Instantiate`/`Destroy`는 **오브젝트 풀링**으로 대체한다. Unity 2021.1+ `ObjectPool<T>` 또는 커스텀 구현. 풀 객체는 반환 시 상태 리셋.

## 필요성

1. **GC 스파이크 방지**: Instantiate/Destroy는 할당/해제 발생
2. **성능**: 총알, 이펙트 등 반복 생성 최적화
3. **초기 로딩**: 미리 Pre-warm으로 지연 제거

## 좋은 예시 ✓

```csharp
using UnityEngine.Pool;

public class BulletManager : MonoBehaviour
{
    [SerializeField] private Bullet _bulletPrefab;
    private IObjectPool<Bullet> _pool;
    
    void Awake()
    {
        _pool = new ObjectPool<Bullet>(
            createFunc: CreateBullet,
            actionOnGet: b => b.gameObject.SetActive(true),
            actionOnRelease: b => b.gameObject.SetActive(false),
            actionOnDestroy: b => Destroy(b.gameObject),
            defaultCapacity: 50,
            maxSize: 200
        );
    }
    
    private Bullet CreateBullet()
    {
        var bullet = Instantiate(_bulletPrefab);
        bullet.Pool = _pool;  // 자기 풀 참조
        return bullet;
    }
    
    public Bullet Fire(Vector3 position)
    {
        var bullet = _pool.Get();
        bullet.transform.position = position;
        return bullet;
    }
}

// ✓ 풀로 반환 시 상태 리셋
public class Bullet : MonoBehaviour
{
    public IObjectPool<Bullet> Pool { get; set; }
    
    void OnEnable()
    {
        // 상태 초기화
        _velocity = Vector3.zero;
        _lifeTime = 0f;
    }
    
    public void Return() => Pool.Release(this);
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 매번 Instantiate/Destroy
public void Fire()
{
    var bullet = Instantiate(_bulletPrefab, transform.position, Quaternion.identity);
    Destroy(bullet, 2f);  // 2초 후 파괴
    // GC 압력 지속 발생
}

// ✗ Bad: 풀 크기 제한 없음
public class BadPool
{
    private Queue<Bullet> _pool = new();
    public void Release(Bullet b) => _pool.Enqueue(b);  // 무한 증가
}
```

## 자동 탐지

```
FOR EACH Instantiate 호출
  IF Update/FixedUpdate 내부
     AND 짧은 수명 객체 (Destroy 호출됨)
  THEN VIOLATION (풀링 권장)
  
FOR EACH pool implementation
  IF 최대 크기 제한 없음
  THEN WARNING
```

## 허용 예외

- 한 번만 생성되는 객체
- 영구 유지되는 객체
- 생성 비용이 낮은 순수 C# 객체
