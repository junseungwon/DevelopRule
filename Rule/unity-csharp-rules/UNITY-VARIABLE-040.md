---
name: 변수 제작
description: 변수명, 스코프, 초기화 시점
category: 규칙
priority: high
---

# UNITY-VARIABLE-040: 변수 제작

## 규칙 정의

변수는 **사용 직전에 선언 + 초기화**. 스코프 최소화. 의미있는 이름 사용(축약 금지). 재사용되지 않으면 `readonly` 고려.

## 필요성

1. **가독성**: 선언과 사용 근접
2. **실수 방지**: 초기화 누락/중복 사용 방지
3. **명확성**: 이름으로 의도 표현

## 좋은 예시 ✓

```csharp
public class GameSystem
{
    // ✓ 의미있는 이름 + 초기화
    private readonly int _maxPlayers = 4;
    private List<Player> _activePlayers = new();
    
    public void ProcessGame()
    {
        // ✓ 사용 직전 선언
        int totalScore = CalculateTotalScore();
        
        // ✓ 스코프 최소화
        if (totalScore > 100)
        {
            int bonusPoints = totalScore / 10;
            ApplyBonus(bonusPoints);
        }
        
        // ✓ foreach 변수는 루프 내 선언
        foreach (var player in _activePlayers)
        {
            player.UpdateScore();
        }
    }
    
    // ✓ out 변수 인라인 선언 (C# 7+)
    public void TryProcess()
    {
        if (TryGetComponent<Rigidbody>(out var rb))
        {
            rb.velocity = Vector3.zero;
        }
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 축약된 이름
public void Process()
{
    int n = 10;     // 무엇의 n?
    var lst = new List<int>();  // 무엇의 리스트?
    float x = 1.5f; // 무엇의 x?
}

// ✗ Bad: 최상단 일괄 선언 (C 스타일)
public void ProcessGame()
{
    int totalScore;
    int bonusPoints;
    Player currentPlayer;
    
    // ... 많은 코드 ...
    
    totalScore = CalculateTotalScore();  // 선언-사용 거리 멀음
}

// ✗ Bad: 초기화 없이 선언
public void Bad()
{
    int value;  // 초기화 누락
    if (condition) value = 10;
    Debug.Log(value);  // CS0165: 할당되지 않은 변수 사용
}

// ✗ Bad: 재할당 없는데 readonly 아님
private List<Player> _players = new();  // 재할당 없으면 readonly
```

## 자동 탐지

```
FOR EACH local variable
  IF 이름 길이 ≤ 2 (i, j, k 제외)
     AND 루프 카운터 아님
  THEN WARNING
  
  IF 초기화 없이 선언
     AND 다음 줄에 할당 아님
  THEN WARNING
  
FOR EACH private field
  IF 생성자 외 재할당 없음
     AND NOT readonly
  THEN WARNING
```

## 허용 예외

- 루프 카운터 (i, j, k)
- 수학적 관례 (x, y, z, dx, dy)
- Lambda 매개변수 단문
