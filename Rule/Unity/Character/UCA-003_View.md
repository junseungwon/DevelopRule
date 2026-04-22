---
name: Unity 캐릭터 아키텍처 — View 규칙
description: MonoBehaviour View 클래스의 해야 할 것과 하면 안 되는 것
category: 규칙
priority: critical
---

# UCA-003: View 규칙 (MonoBehaviour)

## 규칙 정의

View는 Unity 컴포넌트를 조작하고 사용자 입력을 수신하는 **MonoBehaviour**다.
"보여주기"와 "입력 통역"만 담당하며, 판단·계산·상태 저장은 하지 않는다.

---

## 해야 할 것 ✅

- Unity 컴포넌트 조작 (`Animator`, `Image`, `Rigidbody`)
- Unity 생명주기 콜백 수신 (`Awake`, `Update`, `OnTriggerEnter`)
- 사용자 입력 수신 → `event`로 Feature에 전달
- "보여주기" 메서드 제공 (`PlayEat`, `UpdateHpBar`)
- `[SerializeField]`로 인스펙터 참조 노출

---

## 하면 안 되는 것 ❌

- **분기 로직** — "HP 30% 이하면 빨간색" 같은 판단은 Feature에서
- **계산 로직** — 데미지·확률 등 수치 가공 (`*`, `/`)
- **게임 상태 저장** — 표시 상태 외에 보관하지 않는다
- Model 직접 수정 (이벤트로 Feature에 알릴 뿐)
- 다른 View에 직접 접근

---

## 코드 예시

```csharp
public class HumanView : MonoBehaviour
{
    [SerializeField] private Animator animator;
    [SerializeField] private AudioSource audioSource;
    [SerializeField] private AudioClip eatSound;

    // 입력 통로 (View → Feature): 판단 없이 값만 전달
    public event Action<Vector2> OnMoveInput;
    public event Action<bool>    OnRunToggle;
    public event Action          OnEatPressed;

    // 표시 메서드 (Feature → View): 받은 대로만 그린다
    public void SetMoveSpeedNormalized(float v) => animator.SetFloat("Speed", v);
    public void SetRunning(bool isRunning)       => animator.SetBool("IsRunning", isRunning);
    public void PlayEat()
    {
        animator.SetTrigger("Eat");
        audioSource.PlayOneShot(eatSound);
    }

    private void Update()
    {
        OnMoveInput?.Invoke(new Vector2(Input.GetAxis("Horizontal"), Input.GetAxis("Vertical")));
        if (Input.GetKeyDown(KeyCode.LeftShift)) OnRunToggle?.Invoke(true);
        if (Input.GetKeyUp(KeyCode.LeftShift))   OnRunToggle?.Invoke(false);
        if (Input.GetKeyDown(KeyCode.E))         OnEatPressed?.Invoke();
    }
}
```

---

## 안티패턴 — View에 로직 숨기기 ❌

```csharp
// 잘못된 예: View에 분기·계산이 섞임
public void UpdateHpBar(int cur, int max)
{
    hpBar.fillAmount = (float)cur / max;                      // 계산 ❌
    hpBar.color = cur < max * 0.3f ? Color.red : Color.green; // 분기 ❌
}

// 올바른 예: Feature가 이미 계산한 값만 받아서 그린다
public void UpdateHpBar(float ratio) => hpBar.fillAmount = ratio;
public void SetHpBarColor(Color color) => hpBar.color = color;
```

---

## 핵심 체크

- [ ] 분기 로직 없음
- [ ] 계산 로직 없음 (`*`, `/` 같은 수치 가공)
- [ ] 게임 상태 필드 없음
- [ ] Model·Feature 직접 참조 없음
