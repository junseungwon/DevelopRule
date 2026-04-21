---
name: 에러 복구
description: 폴백 전략, Graceful Degradation, 부분 실패 처리
category: 규칙
priority: high
---

# UNITY-RECOVERY-028: 에러 복구

## 규칙 정의

외부 자원(네트워크, 파일, 에셋) 접근 실패 시 **폴백 전략(Graceful Degradation)** 적용. 크래시 대신 대체 경로 또는 기본값 사용.

## 필요성

1. **사용자 경험**: 오류가 게임 전체 중단시키지 않음
2. **안정성**: 부분 실패가 전체 실패로 번지지 않음
3. **복원력**: 네트워크 문제 등에서 회복

## 좋은 예시 ✓

```csharp
public async Task<Texture2D> LoadImageAsync(string url)
{
    try
    {
        return await LoadFromNetwork(url);
    }
    catch (NetworkException)
    {
        Debug.LogWarning($"네트워크 실패, 캐시 시도: {url}");
        try
        {
            return LoadFromCache(url);
        }
        catch (FileNotFoundException)
        {
            return _defaultTexture;  // 폴백
        }
    }
}

// ✓ 재시도 + 지수 백오프
public async Task<T> WithRetryAsync<T>(Func<Task<T>> action, int maxRetries = 3)
{
    for (int i = 0; i < maxRetries; i++)
    {
        try { return await action(); }
        catch (Exception)
        {
            if (i == maxRetries - 1) throw;
            await Task.Delay(TimeSpan.FromSeconds(Math.Pow(2, i)));
        }
    }
    throw new Exception("Unreachable");
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 실패 시 게임 중단
public Texture2D LoadImage(string url)
{
    return LoadFromNetwork(url);  // 예외 시 게임 중단
}

// ✗ Bad: 재시도 로직 없음
public void FetchUserData()
{
    var data = _api.GetUser(_userId);  // 일시적 실패 시 복구 없음
    ShowProfile(data);
}
```

## 자동 탐지

```
FOR EACH 외부 자원 접근 (Network, File, WWW)
  IF try-catch 없음
  THEN VIOLATION
  
  IF 폴백/기본값 제공 없음
  THEN WARNING
```

## 허용 예외

- 필수 자원 로드 실패 시 종료 허용
- 개발 빌드에서 fail-fast 정책
- 테스트에서 예외 검증
