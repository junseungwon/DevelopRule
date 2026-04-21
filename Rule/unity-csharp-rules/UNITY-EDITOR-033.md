---
name: 에디터 확장
description: EditorGUI, Custom Inspector, Editor Tools
category: 규칙
priority: high
---

# UNITY-EDITOR-033: 에디터 확장

## 규칙 정의

에디터 스크립트는 `Assets/Editor/` 폴더에 배치하고 `UnityEditor` 네임스페이스 사용. `SerializedObject`/`SerializedProperty`로 Undo/Redo 지원, `EditorGUI.BeginChangeCheck()`로 변경 감지.

## 필요성

1. **빌드 분리**: Editor 폴더는 빌드 제외
2. **Undo 지원**: 디자이너 작업 보호
3. **데이터 무결성**: 프리팹 오버라이드 추적

## 좋은 예시 ✓

```csharp
// Assets/Editor/EnemyEditor.cs
using UnityEditor;
using UnityEngine;

[CustomEditor(typeof(Enemy))]
public class EnemyEditor : Editor
{
    private SerializedProperty _healthProperty;
    private SerializedProperty _speedProperty;
    
    void OnEnable()
    {
        _healthProperty = serializedObject.FindProperty("_health");
        _speedProperty = serializedObject.FindProperty("_speed");
    }
    
    public override void OnInspectorGUI()
    {
        serializedObject.Update();
        
        EditorGUILayout.PropertyField(_healthProperty);
        EditorGUILayout.PropertyField(_speedProperty);
        
        // ✓ 변경 감지 + Undo
        EditorGUI.BeginChangeCheck();
        bool isElite = EditorGUILayout.Toggle("Elite", ((Enemy)target).IsElite);
        if (EditorGUI.EndChangeCheck())
        {
            Undo.RecordObject(target, "Toggle Elite");
            ((Enemy)target).IsElite = isElite;
        }
        
        serializedObject.ApplyModifiedProperties();
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Assets/Editor 밖에 배치
// Assets/Scripts/EnemyEditor.cs - 빌드에 포함되어 오류

// ✗ Bad: 직접 필드 수정 (Undo 불가)
public override void OnInspectorGUI()
{
    Enemy enemy = (Enemy)target;
    enemy.Health = EditorGUILayout.IntField("Health", enemy.Health);
    // Undo 불가, 프리팹 오버라이드 표시 없음
}

// ✗ Bad: #if UNITY_EDITOR 없이 UnityEditor 참조
using UnityEditor;  // 런타임 코드에서 빌드 오류
public class PlayerController : MonoBehaviour { }
```

## 자동 탐지

```
FOR EACH script using UnityEditor
  IF NOT in Assets/Editor/ folder
     AND NOT wrapped in #if UNITY_EDITOR
  THEN VIOLATION
  
FOR EACH Custom Inspector
  IF target 필드 직접 수정
     AND Undo.RecordObject 없음
  THEN VIOLATION
```

## 허용 예외

- Assembly Definition으로 분리된 Editor 어셈블리
- Runtime 전용 에디터 전처리 코드
