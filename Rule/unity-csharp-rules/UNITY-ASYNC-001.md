---
name: Async 메서드 명명 규칙
description: Task를 반환하는 모든 비동기 메서드는 Async 접미사로 명명되어야 함
category: 명명 규칙
priority: high
---

# UNITY-ASYNC-001: Async 메서드 명명 규칙

## 규칙 정의

비동기 작업을 수행하는 모든 메서드는 **"Async" 접미사**로 명명되어야 한다.

```csharp
메서드명: [동사][명사]Async
예: LoadTextureAsync, InitializeAsync, FetchDataAsync
```

## 필요성

1. **명확한 의도 표현**: 메서드명만으로 비동기 특성을 인식
2. **API 안전성**: 개발자가 블로킹 가능성을 인지
3. **디버깅 용이**: 콜 스택과 로그에서 비동기 작업 추적 가능

## 좋은 예시 ✓

```csharp
public async Task<Texture2D> LoadTextureAsync(string path)
{
    var bytes = await File.ReadAllBytesAsync(path);
    return CreateTextureFromBytes(bytes);
}

public async Task InitializeAsync()
{
    await LoadConfigAsync();
    await LoadAssetsAsync();
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: "Async" 접미사 누락
public async Task<Texture2D> LoadTexture(string path)
{
    var bytes = await File.ReadAllBytesAsync(path);
    return CreateTextureFromBytes(bytes);
}
```

## 자동 탐지

```
IF 메서드가 async 키워드 포함
   AND 메서드명이 "Async"로 끝나지 않음
THEN VIOLATION
```

## 예외

- 오버라이드 메서드 (부모 정의 따름)
- 인터페이스 구현 (계약 준수)
- 테스트 메서드 ([Test] 속성)
