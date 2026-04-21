---
name: Unity C# 규칙 허브
description: 작업 키워드로 적용할 규칙 ID를 즉시 찾는 인덱스
category: 허브
priority: critical
---

# UNITY-HUB-000: Unity C# 규칙 허브

**경로**: `./UNITY-<TOPIC>-<NNN>.md` (총 42개) · **사용법**: 작업 → ID 매칭 → 해당 파일 Read → 적용 → 보고에 `적용 규칙: [ID...]` 명시

## 작업 상황 → 규칙 ID

| 상황 | IDs |
|------|-----|
| 새 클래스/구조체/Enum | 036, 042, 038, 030, 005 |
| MonoBehaviour 작성 | 036, 021, 013, 007, 017 |
| 공용 API · 프로퍼티 | 002, 037, 039, 008, 003 |
| 함수 · 변수 · 네이밍 | 041, 040, 005, 015 |
| 비동기 · 스레드 | 001, 031, 010, 006 |
| 예외 · 입력 · 복구 | 006, 003, 022, 028, 018 |
| 컬렉션 · LINQ · 메모리 | 019, 011, 014, 004 |
| 성능 · 풀링 · 프로파일 | 034, 023, 014, 027 |
| GameObject · Tag/Layer | 013, 035, 021 |
| 싱글톤 · DI · 이벤트 | 016, 020, 017 |
| 리소스 · 파일 I/O | 009, 006, 001 |
| Editor · 플랫폼 분기 | 033, 026, 007, 025 |
| 테스트 | 024, 020, 036 |
| 로그 · 주석 | 012, 008, 029 |
| 네트워크 | 032, 010, 006, 001 |
| Reflection | 027, 014, 023 |

## 규칙 ID 색인

```
001 ASYNC       002 API         003 NULL        004 GENERIC     005 NAMING
006 EXCEPT      007 SERIAL      008 COMMENT     009 RESOURCE    010 THREAD
011 LINQ        012 LOG         013 GAMEOBJ     014 MEMORY      015 CONST
016 SINGLETON   017 EVENT       018 CAST        019 COLLECTION  020 DI
021 LIFECYCLE   022 VALIDATE    023 PROFILE     024 TEST        025 CONFIG
026 CONDITIONAL 027 REFLECT     028 RECOVERY    029 ANNOTATION  030 SOLID
031 TASK        032 NETWORK     033 EDITOR      034 POOL        035 MARKER
036 CLASS       037 PROPERTY    038 ENUM        039 INTERFACE   040 VARIABLE
041 FUNCTION    042 STRUCT
```

> 신규 규칙 추가 시 본 허브의 **상황 표** · **ID 색인** 두 곳 동시 갱신.
