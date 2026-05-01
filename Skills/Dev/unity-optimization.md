# Unity 최적화 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-optimization |
| 역할 | Unity 성능 최적화 전 규칙 참조·최적화 절차 정의 |
| 사용 에이전트 | unity-optimizer, jelly-optimizer, optimization-reviewer |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\` |

---

## 최적화 전 필수 절차 (생략 금지)

### 1단계 — 허브 로드

반드시 Read: `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`

### 2단계 — 성능 관련 핵심 규칙 Read

반드시 참조할 규칙:

| 규칙 ID | 파일명 | 최적화에서의 역할 |
|--------|--------|----------------|
| UNITY-023 | UNITY-PROFILE-023.md | 프로파일링 방법론·측정 없는 최적화 금지 |
| UNITY-014 | UNITY-MEMORY-014.md | 메모리 관리·GC Allocation 감소 |
| UNITY-034 | UNITY-POOL-034.md | 오브젝트 풀링 패턴 |
| UNITY-019 | UNITY-COLLECTION-019.md | 컬렉션 효율적 사용 |
| UNITY-011 | UNITY-LINQ-011.md | LINQ 핫패스 금지 |
| UNITY-027 | UNITY-REFLECT-027.md | Reflection 핫패스 금지 |
| UNITY-021 | UNITY-LIFECYCLE-021.md | Update 병목 감소 |
| UNITY-010 | UNITY-THREAD-010.md | 스레드 안전성·메인 스레드 블로킹 |
| UNITY-031 | UNITY-TASK-031.md | async/await GC 주의사항 |

### 3단계 — 최적화 보고서 작성

보고서 말미에 반드시 명시:
```
적용 규칙: [UNITY-023, UNITY-014, UNITY-034, ...]
```

---

## 최적화 원칙 (규칙 기반)

### 황금 원칙
측정 없는 최적화 금지. 근거 데이터(Profiler 캡처) 또는 합리적 추정 반드시 명시.

### 최적화 순서
1. 알고리즘 — O(n²) → O(n log n) 등 시간 복잡도 개선
2. 자료구조 — 적절한 컬렉션 선택 (UNITY-019)
3. 캐싱 — GetComponent·Find·문자열 PropertyToID 캐싱
4. 풀링 — 스폰/소멸 비용 제거 (UNITY-034)
5. 네이티브/Jobs — 메인 스레드 병목 해소

### 핫패스 금지 항목
- LINQ 사용 (UNITY-011)
- string 연결·포맷 (박싱 발생)
- new 호출 (GC Alloc)
- Reflection (UNITY-027)
- GetComponent·Find 반복 호출 (UNITY-013)
- Renderer.material 직접 접근 (인스턴스 생성)

---

## 최적화 체크리스트

### Update/FixedUpdate 병목
- [ ] 매 프레임 불필요 연산 (캐싱으로 해결 가능한가?)
- [ ] 조건부 로직이 항상 실행되는가?
- [ ] Event 구독 방식으로 전환 가능한가?

### GC Allocation
- [ ] LINQ·람다·클로저 사용 여부
- [ ] 문자열 연결 (StringBuilder 또는 $"" 남용)
- [ ] 박싱 발생 (값 타입 → 참조 타입 캐스팅)
- [ ] 컬렉션 재할당 (List Capacity 미설정)

### 렌더링
- [ ] Renderer.material 직접 접근 → MaterialPropertyBlock으로 교체
- [ ] Shader.PropertyToID 캐싱 여부
- [ ] 드로우콜 과다 (배칭·인스턴싱 기회)
- [ ] 오버드로우 (반투명 객체 정렬)

### 메모리
- [ ] 풀링 미적용 빈번 생성·소멸 오브젝트 (UNITY-034)
- [ ] 대용량 컬렉션 매 프레임 재생성
- [ ] 코루틴·async WaitFor 반복 생성

---

## 최적화 보고서 형식

```
## 병목 목록

### [우선순위 1] {파일명:라인} — {영향도: ms/MB/GC}
  현재: {현재 코드 또는 패턴}
  이슈: {성능 문제 근거}
  개선: {최적화 방안 + 예상 효과}
  코드: {수정 diff 또는 예시}

## 검증 방법
  - {측정 지표}: {검증 절차}

적용 규칙: [UNITY-023, UNITY-014, ...]
```

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| Profiler 데이터 없음 | 정적 분석 기반 추정 + "Profiler 검증 필요" 명시 |
| 개선 후 성능 악화 | 원복 후 원인 재분석 |
| 가독성 vs 성능 충돌 | 10% 미만 개선은 가독성 우선. 충돌 사실 보고 |

---

생성: 2026-05-01
