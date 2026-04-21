---
name: 네트워킹
description: 직렬화, 동기화, 대역폭 최적화
category: 규칙
priority: high
---

# UNITY-NETWORK-032: 네트워킹

## 규칙 정의

네트워크 전송 데이터는 **바이너리 직렬화**(MessagePack, Protobuf) 사용. 송신 빈도 제한(tick rate), 델타 압축 적용. 민감 데이터 암호화.

## 필요성

1. **대역폭 효율**: JSON 대비 50-70% 감소
2. **성능**: 직렬화 속도 향상
3. **보안**: 민감 정보 보호

## 좋은 예시 ✓

```csharp
// ✓ 바이너리 직렬화
[MessagePackObject]
public class PlayerState
{
    [Key(0)] public Vector3 Position;
    [Key(1)] public Quaternion Rotation;
    [Key(2)] public byte Health;  // 0-100이면 byte 충분
}

// ✓ 틱 기반 송신
public class NetworkSender : MonoBehaviour
{
    private const float TICK_INTERVAL = 1f / 30f;  // 30 Hz
    private float _nextTickTime;
    
    void Update()
    {
        if (Time.time >= _nextTickTime)
        {
            SendState();
            _nextTickTime = Time.time + TICK_INTERVAL;
        }
    }
}

// ✓ 델타 압축
public void SendDeltaUpdate()
{
    if (_currentState.Equals(_lastSentState)) return;  // 변경 없으면 스킵
    var delta = CalculateDelta(_lastSentState, _currentState);
    Send(delta);
    _lastSentState = _currentState;
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: JSON 직렬화 (비효율)
void SendState()
{
    var json = JsonUtility.ToJson(_state);  // 텍스트, 크기 큼
    _socket.Send(Encoding.UTF8.GetBytes(json));
}

// ✗ Bad: 매 프레임 송신
void Update()
{
    SendFullState();  // 60 Hz, 대역폭 낭비
}

// ✗ Bad: 평문 전송
void SendLogin(string password)
{
    _socket.Send(Encoding.UTF8.GetBytes(password));
}
```

## 자동 탐지

```
FOR EACH 네트워크 송신
  IF JsonUtility 사용
  THEN WARNING (바이너리 권장)
  
  IF Update 내 송신
     AND tick 제어 없음
  THEN VIOLATION
  
FOR EACH 평문 민감 데이터 (password, token)
  IF 암호화 없이 전송
  THEN VIOLATION
```

## 허용 예외

- 로컬 개발 디버깅
- 사람이 읽을 필요 있는 설정 교환
- HTTPS 보호된 채널
