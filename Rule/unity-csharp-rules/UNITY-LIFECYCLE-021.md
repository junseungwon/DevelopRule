---
name: MonoBehaviour 생명주기
description: Awake, Start, Update, OnDestroy 올바른 활용
category: 규칙
priority: high
---

# UNITY-LIFECYCLE-021: MonoBehaviour 생명주기

## 규칙 정의

생명주기 메서드 책임 분리:
- **Awake**: 자기 자신 초기화, 컴포넌트 캐싱
- **Start**: 다른 객체와의 상호작용 초기화
- **OnEnable/OnDisable**: 이벤트 구독/해제
- **Update**: 프레임 단위 로직
- **OnDestroy**: 정리 작업

## 필요성

1. **실행 순서 보장**: 모든 Awake → 모든 Start
2. **의존성 해결**: 다른 객체 참조는 Start에서
3. **자원 누수 방지**: OnDestroy로 정리

## 좋은 예시 ✓

```csharp
public class Player : MonoBehaviour
{
    private Rigidbody _rb;
    private GameManager _gameManager;
    
    // ✓ Awake: 자기 초기화
    void Awake()
    {
        _rb = GetComponent<Rigidbody>();
    }
    
    // ✓ Start: 외부 참조
    void Start()
    {
        _gameManager = FindObjectOfType<GameManager>();
    }
    
    // ✓ OnEnable: 이벤트 구독
    void OnEnable()
    {
        _gameManager.OnGameOver += HandleGameOver;
    }
    
    // ✓ OnDisable: 이벤트 해제
    void OnDisable()
    {
        if (_gameManager != null)
            _gameManager.OnGameOver -= HandleGameOver;
    }
    
    // ✓ OnDestroy: 최종 정리
    void OnDestroy()
    {
        _rb = null;
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Awake에서 외부 참조
void Awake()
{
    _gameManager = FindObjectOfType<GameManager>();
    // GameManager.Awake가 아직 안 불렸을 수 있음
}

// ✗ Bad: OnEnable에 초기화 로직
void OnEnable()
{
    _rb = GetComponent<Rigidbody>();
    // 여러 번 호출됨 (재활성화 시)
}

// ✗ Bad: OnDestroy에서 자원 정리 누락
void OnDestroy() { }
// 이벤트 구독 해제 안함 - 메모리 누수
```

## 자동 탐지

```
FOR EACH Awake 내부
  IF FindObjectOfType 또는 외부 참조
  THEN WARNING (Start로 이동)
  
FOR EACH OnEnable 내부
  IF GetComponent 호출
  THEN WARNING (Awake로 이동)
  
FOR EACH event += 구독
  IF OnDisable/OnDestroy에 해제 없음
  THEN VIOLATION
```

## 허용 예외

- 정적 싱글톤 접근 (이미 초기화)
- DontDestroyOnLoad 객체 간 참조
- 에디터 전용 코드
