# 디버그 규칙 (JellyMolly 전용)

`DebugController` / `IDebug` / `DebuggableLog` (`Assets/02_Scripts/Debug/Runtime/`) 패턴을 따른다.

## IDebug

- 토글 가능한 `MonoBehaviour`는 구현
- 분기·로그는 `IsDebug == true`일 때만
- `EDebugCategory` 지정 (None / Main / Sub)
- 표시명은 `[DebugSubsystem("이름")]` 또는 `DebugSubsystemName`

## 로그

- 서브시스템 로그는 `Debug.Log` 대신 `DebuggableLog` 사용 (`IsDebug`가 꺼지면 출력 안 함)
- 영역은 `DebugLogArea`: `Debug`, `Jelly`, `Interaction`
- 일반: `LogDebug*` / `LogJelly` / `LogInteraction*` (정적: `LogInteractionStatic`)
- 무거운 문자열은 `Log(..., Func<string>)` 팩토리 오버로드 사용

## 씬

- `DebugController`로 자식 `IDebug` 수집·일괄 적용
- `_scanRoot` 비우면 자기 자식 기준
- 목록 갱신: 인스펙터 「자식에서 IDebug 자동 찾기」 또는 `AutoCollectFromChildren`
- 에디터 즉시 적용 부담 시 `_applyDebugSettingsInPlayModeOnly` 사용

## ⛔ 금지 패턴 목록

- 무조건 `Debug.Log` 남발 → 금지
- `IDebug`와 별도 이중 플래그로 같은 목적 관리 → 금지
