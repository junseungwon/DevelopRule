---
name: 설정 관리
description: 하드코딩 피하기, 설정 파일 구조, 런타임 설정 로드
category: 규칙
priority: high
---

# UNITY-CONFIG-025: 설정 관리

## 규칙 정의

게임 설정값은 **ScriptableObject** 또는 **외부 파일**(JSON, CSV)로 분리한다. 코드 내 매직 넘버 금지. 런타임 변경 가능한 값은 설정 객체로 관리.

## 필요성

1. **디자이너 친화적**: 코드 수정 없이 밸런스 조정
2. **버전 관리**: 설정 변경 추적 용이
3. **환경별 대응**: 개발/프로덕션 설정 분리

## 좋은 예시 ✓

```csharp
// ✓ ScriptableObject 설정
[CreateAssetMenu(menuName = "Configs/EnemyConfig")]
public class EnemyConfig : ScriptableObject
{
    [SerializeField] private float _speed = 5f;
    [SerializeField] private int _health = 100;
    [SerializeField] private float _attackRange = 2f;
    
    public float Speed => _speed;
    public int Health => _health;
    public float AttackRange => _attackRange;
}

public class Enemy : MonoBehaviour
{
    [SerializeField] private EnemyConfig _config;
    
    void Start()
    {
        _currentHealth = _config.Health;
    }
}

// ✓ JSON 설정 로드
public class GameSettings
{
    public static GameSettings Load(string path)
    {
        var json = File.ReadAllText(path);
        return JsonUtility.FromJson<GameSettings>(json);
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 매직 넘버 하드코딩
public class Enemy : MonoBehaviour
{
    void Start()
    {
        _speed = 5.0f;          // 왜 5?
        _health = 100;          // 조정 불가
        _attackRange = 2.5f;    // 디자이너 수정 불가
    }
}

// ✗ Bad: 문자열 경로 하드코딩
public void LoadLevel()
{
    var level = Resources.Load("Levels/level_01_001");  // 오타 취약
}
```

## 자동 탐지

```
FOR EACH 매직 넘버 (리터럴)
  IF 0, 1, -1 이외의 값
     AND const/readonly/SerializeField 아님
  THEN WARNING
  
FOR EACH 문자열 리터럴 경로
  IF "Resources/" 또는 파일 경로
  THEN WARNING (상수화 권장)
```

## 허용 예외

- 명백한 값 (0, 1, -1)
- 수학 상수 (π, e)
- 테스트 코드 내 하드코딩
