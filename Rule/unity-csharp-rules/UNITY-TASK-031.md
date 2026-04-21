---
name: Task/Parallel
description: async/await 확장, Task 체이닝, 병렬 처리
category: 규칙
priority: high
---

# UNITY-TASK-031: Task/Parallel

## 규칙 정의

async 메서드는 `Task` 또는 `Task<T>` 반환(void 금지). CancellationToken 지원, 병렬 실행 시 `Task.WhenAll` 사용. Unity에서는 UniTask 고려.

## 필요성

1. **예외 처리**: async void는 예외 전파 불가
2. **취소 지원**: 장시간 작업 중단 가능
3. **성능**: 독립 작업 병렬화

## 좋은 예시 ✓

```csharp
// ✓ Task 반환, CancellationToken 지원
public async Task<Level> LoadLevelAsync(
    int index, CancellationToken cancellationToken = default)
{
    var data = await LoadDataAsync(index, cancellationToken);
    cancellationToken.ThrowIfCancellationRequested();
    return ParseLevel(data);
}

// ✓ 병렬 실행
public async Task LoadAllAssetsAsync()
{
    var tasks = new[]
    {
        LoadTextureAsync("bg"),
        LoadAudioAsync("music"),
        LoadMeshAsync("player")
    };
    await Task.WhenAll(tasks);
}

// ✓ Timeout + 취소
using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(10));
await FetchDataAsync(cts.Token);
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: async void (예외 처리 불가)
public async void LoadLevel(int index)
{
    await LoadDataAsync(index);
    // 예외 발생 시 AppDomain.UnhandledException으로만 캐치
}

// ✗ Bad: 순차 실행 (병렬 가능한데 비효율)
public async Task LoadAllAssets()
{
    await LoadTextureAsync("bg");
    await LoadAudioAsync("music");
    await LoadMeshAsync("player");
    // 총 시간 = 세 로드의 합계
}

// ✗ Bad: CancellationToken 미지원 장시간 작업
public async Task DoLongOperationAsync()
{
    while (true) { /* 중단 불가 */ }
}
```

## 자동 탐지

```
FOR EACH async method
  IF 반환 타입이 void
     AND NOT event handler
  THEN VIOLATION
  
FOR EACH 순차 await 블록
  IF 독립적인 작업
  THEN WARNING (Task.WhenAll 권장)
  
FOR EACH long-running async method
  IF CancellationToken 매개변수 없음
  THEN WARNING
```

## 허용 예외

- 이벤트 핸들러 (async void 허용)
- Fire-and-forget 작업 (명시적 로깅 필요)
- 매우 짧은 작업 (취소 불필요)
