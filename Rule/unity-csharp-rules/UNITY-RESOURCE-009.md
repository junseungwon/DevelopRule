---
name: 리소스 관리
description: 네이티브 리소스나 파일을 사용하는 클래스는 IDisposable 구현
category: 규칙
priority: high
---

# UNITY-RESOURCE-009: 리소스 관리

## 규칙 정의

네이티브 리소스(파일, 스트림, Texture, Mesh 등)를 사용하는 클래스는 **IDisposable을 구현**하고 `using` 문으로 사용한다. Unity Object는 `Destroy()`로 해제.

## 필요성

1. **메모리 누수 방지**: 관리 외 리소스 명시적 해제
2. **결정적 정리**: GC에 의존하지 않음
3. **자원 경합 방지**: 파일/네트워크 핸들 즉시 해제

## 좋은 예시 ✓

```csharp
// ✓ IDisposable 구현
public class FileProcessor : IDisposable
{
    private FileStream _stream;
    private bool _disposed;
    
    public FileProcessor(string path)
    {
        _stream = File.Open(path, FileMode.Open);
    }
    
    public void Dispose()
    {
        if (_disposed) return;
        _stream?.Dispose();
        _disposed = true;
    }
}

// ✓ using 문 사용
using (var processor = new FileProcessor("data.txt"))
{
    processor.Process();
}

// ✓ Unity Object 해제
void OnDestroy()
{
    if (_renderTexture != null)
        Destroy(_renderTexture);
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: IDisposable 미구현
public class FileProcessor
{
    private FileStream _stream;
    
    public FileProcessor(string path)
    {
        _stream = File.Open(path, FileMode.Open);
        // 해제 안됨 - 파일 핸들 누수
    }
}

// ✗ Bad: using 없이 수동 관리
var stream = File.Open(path, FileMode.Open);
// 예외 발생 시 해제 안됨
stream.Close();
```

## 자동 탐지

```
FOR EACH class
  IF has field of type (FileStream, StreamReader, Texture2D, etc)
     AND NOT implements IDisposable
  THEN VIOLATION
  
FOR EACH IDisposable instance
  IF not used with "using"
     AND not in try-finally
  THEN VIOLATION
```

## 예외

- Unity Object: Destroy() 사용 (IDisposable 대체)
- 장기 수명 리소스: 애플리케이션 전체 수명
