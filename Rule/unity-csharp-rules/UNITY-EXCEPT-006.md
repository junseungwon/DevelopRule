---
name: 예외 처리
description: 모든 예외는 구체적 타입으로 처리되어야 함
category: 규칙
priority: high
---

# UNITY-EXCEPT-006: 예외 처리

## 규칙 정의

모든 예외는 **구체적 타입**으로 처리되어야 한다. 일반 `Exception` 캐치는 금지하며, 예상 가능한 예외 타입을 명시한다.

```csharp
catch (SpecificException ex)  // ✓ 구체적 타입
catch (Exception ex)          // ✗ 너무 일반적
```

## 필요성

1. **명확한 오류 처리**: 어떤 예외를 처리하는지 명시
2. **버그 은폐 방지**: 예상치 못한 예외를 삼키지 않음
3. **디버깅 용이**: 예외 원인 추적 가능

## 좋은 예시 ✓

```csharp
public async Task<Texture2D> LoadTextureAsync(string path)
{
    try
    {
        var bytes = await File.ReadAllBytesAsync(path);
        return CreateTexture(bytes);
    }
    catch (FileNotFoundException ex)
    {
        Debug.LogError($"파일 없음: {path}");
        return null;
    }
    catch (IOException ex)
    {
        Debug.LogError($"IO 오류: {ex.Message}");
        return null;
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 일반 Exception 캐치
public async Task<Texture2D> LoadTexture(string path)
{
    try
    {
        var bytes = await File.ReadAllBytesAsync(path);
        return CreateTexture(bytes);
    }
    catch (Exception ex)  // 모든 예외를 덮음
    {
        return null;
    }
}

// ✗ Bad: Swallow (빈 catch)
try { DoWork(); }
catch { }
```

## 자동 탐지

```
FOR EACH catch block
  IF catch type is "Exception"
     AND NOT rethrown
  THEN VIOLATION
  
  IF catch block is empty
  THEN VIOLATION
```

## 예외

- 최상위 핸들러: main loop 또는 이벤트 핸들러
- Rethrow: `catch (Exception) { Log(); throw; }` 허용
