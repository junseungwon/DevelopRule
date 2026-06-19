# jelly-icon-artist / unity-developer 평가 기록 (9:16 목표 이미지 재현 + Molly 젤리)

- 날짜: 2026-05-23
- 태스크: 9:16(1080×1920) 베이스, 목표 이미지대로 메인 홈 UI 완성 + 파란 광택 젤리 Molly(표정) 제작
- 코드 규칙 점수: A
- 최적화 점수: A
- 종합 점수: A

## Molly 구현 방식
- jelly-icon-artist 절차적 PNG 채택: 512px 파란 광택 젤리(동심원 음영 + 좌상단 소프트 하이라이트 + 분홍 볼 + 까만 눈 + 슬픈 입 호) + 별도 바닥 그림자 PNG.
- UI 이미지(stage)로 표시 — 씬/3D 변경 0(파일 기반). idle bob 은 HomeUIController 가 translate(px)+미세 스쿼시로 트윈.

## 세부 평가
- 목표 재현도: A — 노치(검은 알약), 상단(설정/타이틀+HOME/level-pill/coin·gem), 스탯3(하트 틴트+구간색 바+%/깜빡), CLEAR TOYS 배지, 5버튼 컬러타일+준비중, 파란 광택 젤리 Molly+표정+그림자. 9:16 비례 환산.
- 코드: A — UpdateMollyBob translate(-50%, bob px)+scale, Length+ 연산 제거(컴파일 안전). name 매핑/url 전수 일치.
- 아이콘 생성기: A — Blend 경계검사 t.width/height 기반(128/512 호환), SaveBig(maxTextureSize 1024), SoftBlob/EllipseFill/DrawArc 헬퍼.

## GUID/매핑 검증
- molly/molly-shadow/notch UXML↔컨트롤러 Q OK, molly_jelly/molly_shadow IconGen↔USS url OK.
- 기존 아이콘 9종 PNG 존재(이전 메뉴 실행 이력), 에디터 로그 최근 컴파일 에러 0.

## 리스크 / 미검증
- Molly PNG(molly_jelly/molly_shadow) 미생성 → 메뉴 JellyMolly/Generate Icons 재실행 필요(9 아이콘 덮어쓰기 + Molly 2종 신규).
- 절차적 젤리 = 단순 플랫(고품질 일러스트 아님). 추후 디자인 에셋 교체 가능.
- UI Molly(불투명)가 3D Molly 위 Overlay 로 덮음 → 3D MeshRenderer 미변경(씬 최소 변경 원칙). 터치는 SphereCollider 로 동작.
- 실제 렌더: GUI 에디터 점유로 배치 차단 → 메뉴 실행 필요(은폐 없음, 라이선스 정상).
