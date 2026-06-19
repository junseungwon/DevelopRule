# jelly-icon-artist / unity-developer 평가 기록 (HTML 충실 재현 + 아이콘 제작)

- 날짜: 2026-05-23
- 태스크: UI Toolkit 화면을 HTML 데모(ui-demo.html) 충실 재현 + 실제 이미지 아이콘 절차적 제작
- 코드 규칙 점수: A
- 최적화 점수: A
- 종합 점수: A

## 전용 에이전트 구성 (CTO 승인)
- jelly-icon-artist 신설: `C:\DevelopRule\agents\jelly-team\icon-artist.md`
- 방식: 에디터 스크립트 Texture2D 절차적 PNG(외부 의존 0, 신뢰성 우선)

## 세부 평가
- HTML 재현도: A — 색상 토큰 12종 정확, 390→1080 비례 환산(2.77x). 스탯 바 height33 radius22 bg#e3e7ef + 구간색 fill(70+초록/30~69노랑/0~29빨강), 하트 틴트, 5버튼 컬러타일+radius, exercise opacity.5+준비중 태그, 토스트, CLEAR TOYS 배지, stage 투명(3D Molly 비침).
- 아이콘 제작: A — IconGenerator 9종(cookie/burger/dance/runner/cart/heart/coin/gem/settings), AA 드로잉 헬퍼(원/둥근사각/하트/다이아/라인), TextureImporter Sprite/UI 설정, 팔레트 토큰 일관.
- 컨트롤러: A — UXML name↔Q 매핑 21/21, ApplyStat name 인자 제거(미사용 정리), 라이프사이클 함수 분리, 틴트/구간색/깜빡임/쿨타임 유지. GameEvents/Feeding/Dance 연동 유지.

## GUID/매핑 검증
- name 매핑 21/21 OK, 아이콘 IconGen↔USS url 9/9 OK.
- IconGenerator.cs.meta(d4e5f6..) 생성. USS url 경로 참조(GUID 비의존).

## 리스크 / 미검증
- UI Toolkit 미지원: inset/conic gradient → 단색/카운트다운 대체(경미 시각 차이).
- 아이콘 PNG 미생성, UXML/USS 재임포트, 실제 렌더: GUI 에디터 프로젝트 점유로 배치 차단 → 미수행(에디터 강제종료 안 함, 라이선스 정상, 최근 컴파일 에러 0).
- 완성: 에디터 메뉴 JellyMolly/Generate Icons 실행(아이콘 생성) 후 Home.unity Play.
