---
name: Unity 캐릭터 아키텍처 — ScriptableObject Config 규칙
description: SO Config 클래스의 역할·해야 할 것·하면 안 되는 것
category: 규칙
priority: high
---

# UCA-005: ScriptableObject Config 규칙

## 규칙 정의

ScriptableObject Config는 **설계 시점 데이터 컨테이너**다.
밸런싱 수치·애니 파라미터·초기 스탯처럼 에디터에서 조정하는 값만 담는다.
런타임 상태 저장소도 아니고, 로직을 담는 곳도 아니다.

---

## SO에 넣어야 하는 것 ✅

- 걷기/뛰기 속도·스태미나 소모율 같은 **수치**
- 먹기 지속 시간·회복량 같은 **밸런싱 값**
- 최대 체력·최대 스태미나 같은 **초기값**
- 애니메이션 블렌드 값·이펙트 프리팹 참조

## SO에 넣으면 안 되는 것 ❌

- **런타임 상태** (`currentHp`, `isEating`) → Model 또는 Feature 내부
- **계산 로직** (데미지 공식) → POCO 서비스로
- **게임 진행 상태** (현재 레벨·진행도) → Model
- **씬 간 공유 데이터** (플레이어 소유 아이템) → 전용 SaveData POCO

---

## SO 사용 원칙

- Feature·Model은 SO를 **읽기만** 한다 (수정 금지)
- 가능하면 `[field: SerializeField]`와 `private set`으로 보호
- Feature 생성자로 SO를 **주입**하고 필드로 보관
- Model 생성자에서 SO 값을 **복사**해서 초기화 (참조 보관 지양)

---

## 코드 예시

```csharp
[CreateAssetMenu(fileName = "MovementConfig", menuName = "Human/Movement Config")]
public class MovementConfig : ScriptableObject
{
    [Header("Walk")]
    public float walkSpeed = 2.5f;

    [Header("Run")]
    public float runSpeed = 6f;
    public float runStaminaCostPerSec = 15f;

    [Header("Recovery")]
    public float staminaRecoveryPerSec = 10f;
}
```

## 안티패턴 — SO에 런타임 상태 저장 ❌

```csharp
[CreateAssetMenu]
public class PlayerStats : ScriptableObject
{
    public float currentHp;  // ❌ 에셋이라 모든 인스턴스가 공유됨
}
```

---

## SO 덕분에 가능해지는 것

- 같은 Feature 코드를 **다른 수치 에셋**으로 재사용 (Human/Zombie/Cheetah)
- 기획자가 코드 재컴파일 없이 수치 조정
- 난이도별 프리셋 교체
- 테스트에서 `ScriptableObject.CreateInstance<T>()`로 임의 값 주입

---

## 핵심 체크

- [ ] 런타임 상태 필드 없음 (`currentXxx`, `isXxx`)
- [ ] 계산 메서드 없음 (로직 아님)
- [ ] 기획자가 읽기 쉬운 `[Header]` 그룹화
