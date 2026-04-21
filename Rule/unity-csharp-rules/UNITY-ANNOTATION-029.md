---
name: 메타 주석
description: TODO, FIXME, HACK, NOTE 사용법, 만료 주석 처리
category: 규칙
priority: high
---

# UNITY-ANNOTATION-029: 메타 주석

## 규칙 정의

메타 주석 표준 사용:
- **TODO**: 추후 구현할 기능
- **FIXME**: 수정 필요한 버그
- **HACK**: 임시 우회 코드 (정상화 계획 포함)
- **NOTE**: 중요한 설명

형식: `// TYPE(담당자, 날짜): 내용`

## 필요성

1. **추적 가능성**: 미완성 작업 검색 용이
2. **책임 명확화**: 담당자/기한 명시
3. **기술 부채 가시화**: HACK 관리

## 좋은 예시 ✓

```csharp
public class Player : MonoBehaviour
{
    void Update()
    {
        // TODO(alice, 2026-05-01): 입력 시스템을 새 Input System으로 이전
        ProcessLegacyInput();
        
        // FIXME(bob, 2026-04-30): 점프 중 벽 클립 발생 - 충돌 검사 수정 필요
        if (_isJumping && _velocity.y > 0)
        {
            CheckCollision();
        }
        
        // HACK(charlie, 2026-05-15): 서버 동기화 지연 회피용 임시 코드, v1.2 출시 후 제거
        if (_networkManager.Latency > 200)
        {
            UseLocalPrediction();
        }
        
        // NOTE: Time.deltaTime은 프레임 간 시간이며, fixedDeltaTime과 다름
        _position += _velocity * Time.deltaTime;
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 담당자/날짜 없음
// TODO: 개선
void BadMethod() { }

// ✗ Bad: 설명 부족
// FIXME: 버그
void BuggyMethod() { }

// ✗ Bad: HACK인데 계획 없음
// HACK: 임시로 해둠
void HackedMethod() { }

// ✗ Bad: 오래 방치된 TODO (만료)
// TODO: 2023년에 수정하기 (지금은 2026년)
void StaleMethod() { }
```

## 자동 탐지

```
FOR EACH TODO/FIXME/HACK 주석
  IF 담당자 표기 없음
  THEN WARNING
  
  IF 날짜 표기 없음
  THEN WARNING
  
  IF 6개월 이상 미해결
  THEN WARNING (만료 검토)
```

## 허용 예외

- 명백한 단순 TODO (테스트 코드 등)
- 외부 이슈 트래커로 관리되는 경우
