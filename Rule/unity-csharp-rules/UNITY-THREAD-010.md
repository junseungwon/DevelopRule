---
name: 스레드 안전성
description: 여러 스레드에서 접근하는 공유 상태는 적절한 동기화 메커니즘으로 보호
category: 규칙
priority: high
---

# UNITY-THREAD-010: 스레드 안전성

## 규칙 정의

여러 스레드에서 접근하는 공유 상태는 **동기화 메커니즘**(lock, Interlocked, ConcurrentCollection)으로 보호한다. Unity API는 메인 스레드에서만 호출.

## 필요성

1. **데이터 무결성**: Race condition 방지
2. **Unity 규약**: Unity API는 메인 스레드 전용
3. **예측 가능성**: 동시성 버그 제거

## 좋은 예시 ✓

```csharp
public class ThreadSafeCounter
{
    private int _count;
    private readonly object _lock = new();
    
    public int Increment()
    {
        lock (_lock)
        {
            return ++_count;
        }
    }
    
    // ✓ Interlocked (원자적 연산)
    public int FastIncrement()
    {
        return Interlocked.Increment(ref _count);
    }
}

// ✓ Concurrent 컬렉션
private ConcurrentQueue<Data> _queue = new();

// ✓ 메인 스레드 디스패치
await Task.Run(() => ProcessData());
UnityMainThreadDispatcher.Enqueue(() => transform.position = newPos);
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 동기화 없는 공유 상태
public class Counter
{
    private int _count;
    public int Increment() => ++_count;  // Race condition
}

// ✗ Bad: 백그라운드 스레드에서 Unity API 호출
Task.Run(() =>
{
    transform.position = Vector3.zero;  // 예외 발생
});
```

## 자동 탐지

```
FOR EACH field accessed from multiple threads
  IF NOT volatile
     AND NOT accessed within lock
     AND NOT concurrent collection
  THEN WARNING

FOR EACH Unity API call
  IF called from Task.Run or Thread
  THEN VIOLATION
```

## 예외

- 읽기 전용 불변 데이터: 동기화 불필요
- ThreadLocal<T> 사용 필드: 스레드별 독립
