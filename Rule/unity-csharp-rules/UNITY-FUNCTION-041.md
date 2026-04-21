---
name: 함수 제작
description: 함수 크기, 매개변수 수, 단일 책임
category: 규칙
priority: high
---

# UNITY-FUNCTION-041: 함수 제작

## 규칙 정의

함수는 **단일 책임**, 줄 수 **50줄 이하**, 매개변수 **4개 이하**. 동사+명사 명명. Boolean 매개변수 대신 열거형 또는 별도 메서드.

## 필요성

1. **이해 용이**: 작은 함수는 한눈에 파악
2. **재사용성**: 범용 단위로 분리
3. **테스트 용이**: 작은 단위 테스트 가능

## 좋은 예시 ✓

```csharp
// ✓ 단일 책임 + 동사+명사
public void SpawnEnemy(Vector3 position)
{
    var enemy = _pool.Get();
    enemy.transform.position = position;
    enemy.Initialize();
}

// ✓ 매개변수 4개 이하
public Enemy CreateEnemy(EnemyType type, Vector3 position, int level)
{
    var enemy = SpawnEnemy(type, position);
    enemy.SetLevel(level);
    return enemy;
}

// ✓ 많은 매개변수는 객체로 묶기
public class EnemySpawnConfig
{
    public EnemyType Type;
    public Vector3 Position;
    public int Level;
    public float Scale;
    public Color Color;
}
public Enemy CreateEnemy(EnemySpawnConfig config) { /* ... */ }

// ✓ Boolean 대신 별도 메서드
public void ShowUI() { /* ... */ }
public void HideUI() { /* ... */ }
// SetUIVisibility(bool)보다 명확
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 긴 함수 (여러 책임)
public void ProcessPlayer()
{
    // 매개변수 검증 20줄
    // 이동 계산 30줄
    // 애니메이션 업데이트 20줄
    // 공격 처리 30줄
    // 총 100줄 넘음
}

// ✗ Bad: 너무 많은 매개변수
public Enemy SpawnEnemy(
    EnemyType type, Vector3 position, Quaternion rotation,
    int level, float scale, Color color, bool isBoss, string name)
{
    // 7+ 매개변수 - 호출 시 순서 실수 위험
}

// ✗ Bad: Boolean 매개변수로 동작 분기
public void SetState(bool isActive)
{
    if (isActive) Activate();
    else Deactivate();
}
// → Activate() / Deactivate() 별도 메서드

// ✗ Bad: 명명 모호
public void Process() { /* ... */ }  // 무엇을 처리?
public void Handle() { /* ... */ }   // 무엇을 핸들?
```

## 자동 탐지

```
FOR EACH method
  IF 줄수 > 50
  THEN WARNING
  
  IF 매개변수 수 > 4
  THEN WARNING
  
  IF Boolean 매개변수 + 상태 분기
  THEN WARNING
  
  IF 이름이 동사 아님
     AND 생성자/프로퍼티 아님
  THEN WARNING
```

## 허용 예외

- 생성자 (특히 DI 컨테이너)
- 팩토리 메서드
- Unity 이벤트 시그니처 고정 메서드
