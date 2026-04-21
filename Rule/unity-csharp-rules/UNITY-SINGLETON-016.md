---
name: 싱글톤 패턴
description: 싱글톤 구현 방식, 생명주기 관리, 테스트 가능성
category: 규칙
priority: high
---

# UNITY-SINGLETON-016: 싱글톤 패턴

## 규칙 정의

싱글톤은 **진정으로 전역 상태가 필요한 경우**에만 사용한다. MonoBehaviour 싱글톤은 생명주기 명시적 관리(Awake 등록, OnDestroy 해제), 인터페이스 기반 설계로 테스트 가능성 확보.

## 필요성

1. **테스트 가능성**: 인터페이스 추상화로 모의 객체 주입 가능
2. **생명주기 안정성**: 씬 전환 시 명확한 초기화/파괴
3. **전역 상태 제한**: 남용 방지

## 좋은 예시 ✓

```csharp
// ✓ 인터페이스 기반 MonoBehaviour 싱글톤
public interface IGameManager
{
    int Score { get; }
    void AddScore(int points);
}

public class GameManager : MonoBehaviour, IGameManager
{
    public static IGameManager Instance { get; private set; }
    
    public int Score { get; private set; }
    
    void Awake()
    {
        if (Instance != null && Instance != (IGameManager)this)
        {
            Destroy(gameObject);
            return;
        }
        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
    
    void OnDestroy()
    {
        if (Instance == (IGameManager)this) Instance = null;
    }
    
    public void AddScore(int points) => Score += points;
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 정리 없는 싱글톤
public class BadManager : MonoBehaviour
{
    public static BadManager Instance;
    
    void Awake()
    {
        Instance = this;  // 중복 체크 없음, DontDestroyOnLoad 없음
    }
    // OnDestroy 없음 - 씬 전환 시 null 참조
}

// ✗ Bad: 테스트 불가능한 강한 결합
public class Player : MonoBehaviour
{
    void Update()
    {
        BadManager.Instance.DoSomething();  // 모의 불가
    }
}
```

## 자동 탐지

```
FOR EACH static Instance 필드
  IF Awake 내 null 체크 없음
  THEN VIOLATION
  
  IF OnDestroy 내 Instance = null 없음
  THEN WARNING
  
  IF Instance 타입이 인터페이스 아님
     AND 테스트 가능성 필요
  THEN WARNING
```

## 허용 예외

- 순수 C# 클래스 Lazy<T> 싱글톤
- 에디터 도구 싱글톤
- 내부 전용 유틸리티
