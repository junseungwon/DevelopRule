---
name: 조건부 컴파일
description: #if UNITY_EDITOR, 플랫폼별 코드, 디버그 심볼
category: 규칙
priority: high
---

# UNITY-CONDITIONAL-026: 조건부 컴파일

## 규칙 정의

플랫폼/환경별 코드는 `#if` 지시문으로 분리. 에디터 전용 코드는 `#if UNITY_EDITOR`, 성능 크리티컬 디버그는 `[Conditional("DEBUG")]` 속성 사용.

## 필요성

1. **플랫폼 호환성**: 특정 플랫폼 API 사용 시 필수
2. **빌드 크기 최소화**: 프로덕션 제거 가능
3. **에디터 전용 기능 보호**: 런타임 빌드 오류 방지

## 좋은 예시 ✓

```csharp
using System.Diagnostics;

public class DebugTools : MonoBehaviour
{
    // ✓ 에디터 전용 메서드
    #if UNITY_EDITOR
    [UnityEditor.MenuItem("Tools/Reset Save")]
    static void ResetSave() { /* ... */ }
    #endif
    
    // ✓ 플랫폼별 분기
    void Connect()
    {
        #if UNITY_ANDROID
            AndroidJNIHelper.Initialize();
        #elif UNITY_IOS
            IOSNetwork.Connect();
        #else
            DefaultConnect();
        #endif
    }
    
    // ✓ Conditional 속성 (호출 자체가 제거됨)
    [Conditional("DEBUG")]
    public static void DebugLog(string message)
    {
        UnityEngine.Debug.Log(message);
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 런타임 체크 (빌드 크기 불변)
void Start()
{
    if (Application.isEditor)
    {
        ResetDebugData();  // 빌드에 여전히 포함됨
    }
}

// ✗ Bad: 과도한 #if 중첩
#if UNITY_EDITOR
    #if UNITY_2020_1_OR_NEWER
        #if !UNITY_ANDROID
            // 가독성 저하
        #endif
    #endif
#endif

// ✗ Bad: 에디터 API 런타임 사용 시도
void Update()
{
    UnityEditor.AssetDatabase.Refresh();  // 빌드 시 오류
}
```

## 자동 탐지

```
FOR EACH UnityEditor 네임스페이스 사용
  IF #if UNITY_EDITOR로 감싸지지 않음
  THEN VIOLATION
  
FOR EACH if (Application.isEditor) 런타임 체크
  THEN WARNING (#if 권장)
  
FOR EACH #if 중첩 3단계 이상
  THEN WARNING (가독성)
```

## 허용 예외

- 런타임에 플랫폼 동적 전환 필요 시
- 테스트 코드 내 분기
