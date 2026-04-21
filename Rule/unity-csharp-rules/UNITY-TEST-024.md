---
name: 테스트 코드
description: 단위 테스트 작성, AAA 패턴, 테스트 명명
category: 규칙
priority: high
---

# UNITY-TEST-024: 테스트 코드

## 규칙 정의

테스트 메서드는 **AAA 패턴**(Arrange-Act-Assert) 따름. 테스트 이름은 `메서드명_조건_예상결과` 형식. 단위 테스트는 `[Test]`, 코루틴 테스트는 `[UnityTest]`.

## 필요성

1. **가독성**: 테스트 의도 즉시 파악
2. **일관성**: 팀 전체 동일한 구조
3. **실패 분석**: 어느 단계에서 실패했는지 명확

## 좋은 예시 ✓

```csharp
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;

public class PlayerTests
{
    // ✓ 메서드명_조건_결과
    [Test]
    public void ApplyDamage_NegativeAmount_ThrowsException()
    {
        // Arrange
        var player = new Player();
        
        // Act & Assert
        Assert.Throws<ArgumentException>(() => player.ApplyDamage(-10));
    }
    
    [Test]
    public void ApplyDamage_ValidAmount_ReducesHealth()
    {
        // Arrange
        var player = new Player { Health = 100 };
        
        // Act
        player.ApplyDamage(30);
        
        // Assert
        Assert.AreEqual(70, player.Health);
    }
    
    // ✓ 코루틴 테스트
    [UnityTest]
    public IEnumerator LoadScene_ValidName_CompletesLoading()
    {
        yield return SceneManager.LoadSceneAsync("TestScene");
        Assert.AreEqual("TestScene", SceneManager.GetActiveScene().name);
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 모호한 이름
[Test]
public void Test1()
{
    var p = new Player();
    p.ApplyDamage(10);
    Assert.IsTrue(p.Health < 100);
}

// ✗ Bad: 한 테스트에 여러 시나리오
[Test]
public void ApplyDamageTests()
{
    // 음수, 0, 정상 값 모두 테스트 - 실패 원인 불명확
}

// ✗ Bad: Assert 없음
[Test]
public void CreatePlayer()
{
    var p = new Player();
    p.Spawn();
    // Assert 누락
}
```

## 자동 탐지

```
FOR EACH method with [Test]/[UnityTest]
  IF 이름이 "Test1", "TestCase" 등 모호
  THEN VIOLATION
  
  IF Assert 호출 없음
  THEN VIOLATION
  
  IF 한 테스트에 여러 Assert 그룹
  THEN WARNING (분리 권장)
```

## 허용 예외

- 통합 테스트 (여러 assert 불가피)
- 파라미터화된 테스트 ([TestCase])
