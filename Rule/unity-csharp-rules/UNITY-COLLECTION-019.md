---
name: 컬렉션 선택
description: List vs Array vs Queue, 성능과 기능 고려
category: 규칙
priority: high
---

# UNITY-COLLECTION-019: 컬렉션 선택

## 규칙 정의

용도에 맞는 컬렉션 선택: Array(고정 크기), List<T>(동적), HashSet<T>(중복 제거), Dictionary<K,V>(조회), Queue/Stack<T>(FIFO/LIFO).

## 필요성

1. **성능 최적화**: 작업 특성에 맞는 자료구조
2. **메모리 효율**: 불필요한 용량/할당 방지
3. **의도 명확화**: 코드로 사용 의도 파악

## 좋은 예시 ✓

```csharp
private readonly GameObject[] _slots = new GameObject[9];
private List<Enemy> _enemies = new(100);

private Dictionary<int, Player> _players = new();
public Player GetPlayer(int id) => _players.TryGetValue(id, out var p) ? p : null;

private HashSet<string> _visitedScenes = new();
if (_visitedScenes.Add(sceneName)) { /* 처음 방문 */ }

private Queue<Command> _commandQueue = new();
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: List에서 조회 (O(n))
private List<Player> _players = new();
public Player GetPlayer(int id)
{
    return _players.FirstOrDefault(p => p.Id == id);
}

// ✗ Bad: HashSet 대신 List + Contains
if (!_tags.Contains(tag)) _tags.Add(tag);

// ✗ Bad: 초기 용량 미설정
private List<Enemy> _enemies = new();
```

## 자동 탐지

```
FOR EACH List<T>.Contains 빈번 호출
  THEN WARNING (HashSet 권장)
  
FOR EACH List<T>.FirstOrDefault(key 기반)
  THEN WARNING (Dictionary 권장)
```

## 허용 예외

- 작은 컬렉션 (< 10)
- 순차 접근만 필요
- 프로파일링으로 확인된 경우
