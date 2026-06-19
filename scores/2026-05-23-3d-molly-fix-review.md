# unity-developer / jelly-icon-artist 평가 기록 (3D Molly 개조 + 2D 제거, 배치 실검증)

- 날짜: 2026-05-23
- 태스크: 2D Molly 제거 + 3D Molly 개조(파랑/표정/위치) — 에디터 닫힘 → 배치 모드 실제 실행·검증
- 코드 규칙 점수: A
- 최적화 점수: A
- 종합 점수: A

## 실제 배치 컴파일 결과 (이번엔 실검증)
- Unity 배치(-executeMethod Jelly3DFix.Apply) EXIT 0. error CS / Exception 0건. script compilation 3.4s 클린.
- 2D Molly 제거(UXML/USS/Controller) + 신규 스크립트(Jelly3DFix) 모두 정상 빌드.

## 3D Molly 개조 내역 (배치로 씬/머티리얼 실변경 검증)
- 머티리얼: Molly_Mat _BaseColor=(0.34,0.71,0.94) #57b4f0 파랑 반영 확인.
- 표정: Molly(2001000) 자식 Face 아래 Eye_L/Eye_R(위), Mouth(아래), Cheek_L/Cheek_R 6요소 생성. -Z(카메라쪽) 표면 z=-0.52 배치. Molly_Face_Mat(URP Unlit 검정, guid 650dd95)·Molly_Cheek_Mat(분홍) 신규 생성. 콜라이더 제거(터치는 부모 SphereCollider).
- 위치/크기: Molly pos(0,-0.95,0) scale1.25, Camera pos(0,0.2,-4.6) rot6° FOV55 → stage 영역(중앙-하단)으로 이동, 상단 스탯 비가림.

## 2D 제거 확인
- UXML molly/molly-shadow 요소 0, USS .molly 규칙 0, Controller molly 캐싱/UpdateMollyBob 0. 노치 유지. name↔Q 매핑 무결.

## 정합성 검증
- Face 자식 m_Father=2001001(Molly Transform) 정확. Face_Mat 셰이더 할당 정상(not found 경고 0). 배치 로그 경고 0.

## 리스크 / 잔여
- 입은 가로 바로 단순화(슬픈 호는 3D 프리미티브 한계). Unlit이라 얼굴 음영 없음(의도, 표정 가독성).
- 본체는 URP Lit 파랑(광택은 머티리얼 스무스니스 의존) — 2D PNG 같은 강한 하이라이트는 아님.
- UI Molly 제거로 stage 투명 → 3D Molly 가 Overlay UI 아래 정상 표시.
- 실제 Play 캡처는 미수행(배치 플레이모드 캡처 생략). 사용자가 에디터로 Home.unity 열어 육안 확인 권장.
