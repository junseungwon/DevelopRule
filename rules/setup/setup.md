# 초반 세팅 규칙

작업 시작 전 아래 항목을 확인한다:

## 0. Claude 사용량 모니터 실행

새 세션 시작 시 아래 명령어로 토큰 사용량 모니터를 백그라운드 터미널에서 실행한다.

```bash
claude-monitor
# 또는 단축 명령어
cmonitor
```

> 플러그인 위치: `C:\DevelopRule\plugins\Claude-Code-Usage-Monitor`
> 미설치 시: `pip install claude-monitor` 또는 `uv tool install claude-monitor`

---

## 1. 기본 환경 확인

1. 현재 작업 디렉토리 및 Git 브랜치 확인
2. 관련 파일 읽기 (수정 전 반드시 Read 도구로 먼저 읽기)
3. 파괴적 작업(삭제, 강제 푸시 등) 전 사용자 확인
