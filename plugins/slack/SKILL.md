---
name: slack
description: "Slack 연동 스킬. Claude Code에서 Slack으로 메시지 전송, 알림, 워크플로우 관리. /slack 명령어로 Slack 채널에서 Claude 명령 실행, @claude 멘션으로 대화, 작업 완료/GitHub/rule-developer 결과를 자동 공유. Slack 메시지 작성 시, 파일 업로드, 스레드 관리, 반응 추가 등 모든 Slack 작업을 수행."
---

# Slack 연동 스킬

Claude Code와 Slack을 양방향으로 연동하는 완전한 통합 스킬.

## 개요

```
Claude Code              Slack
    ↔️ (양방향)
메시지/파일      ↔️      메시지/파일
작업 완료        →       알림
명령어 실행      ←       /slack 명령어
```

---

## 🎯 핵심 기능

### 1. Claude → Slack (자동 알림)

#### 1-1. 작업 완료 알림
```
작업 완료 시 #claude-notifications 채널로 자동 전송:

✅ 작업명: rule-developer 분석 완료
📄 파일: code-analysis-report.md
⏱️ 소요시간: 5분 30초
📊 상세: Analyzer phase 완료
```

#### 1-2. GitHub 이벤트 알림
```
#github-events 채널로 자동 전송:

🔔 PR 열림: "feature/auth" (user123)
   링크: https://github.com/owner/repo/pull/123
   내용: Add authentication middleware

📍 Issue 닫힘: "Fix login bug" #456
```

#### 1-3. rule-developer 결과 공유
```
#rule-developer 채널로 자동 전송:

📋 분석 완료: GitHub Repository
├─ code-analysis-report.md ✅
├─ rule-design-spec.md ✅
├─ developer-agent-spec.md ✅
└─ validation-report.md ✅

⚠️ Critical: 0 | Major: 2 | Minor: 5
```

### 2. Slack → Claude (양방향 제어)

#### 2-1. /slack 명령어
```
/slack run <명령어>
/slack ask <질문>
/slack analyze <파일경로>
/slack report <기간>
```

예:
```
/slack run rule-developer를 분석해줘: https://github.com/owner/repo
→ Claude가 rule-developer 하네스 실행 후 결과 공유

/slack ask JellyMolly 프로젝트의 성능 병목이 뭔가요?
→ Claude가 분석 후 스레드로 답변

/slack analyze Assets/02_Scripts/Character/Jelly/JellyPlayer.cs
→ Claude가 파일 분석 후 결과 공유
```

#### 2-2. @claude 멘션
```
@claude 이 코드 리뷰해줄래?
```
스레드에서 자동으로 Claude가 응답.

#### 2-3. 파일 업로드
```
Slack에서 파일 업로드 + "분석해줘" 메시지
→ Claude가 파일 분석 후 결과 스레드로 공유
```

---

## 📋 필수 설정

### 1. Slack Bot Token 등록
`.claude/settings.json`:
```json
{
  "slack": {
    "enabled": true,
    "bot_token": "xoxb-YOUR-BOT-TOKEN-HERE",
    "default_channel": "#claude-code",
    "channels": {
      "notifications": "#claude-notifications",
      "github": "#github-events",
      "rule_developer": "#rule-developer"
    }
  }
}
```

### 2. Slack App 권한 (Scopes)
```
chat:write              메시지 전송
channels:read           채널 읽기
users:read              사용자 정보
reactions:write         반응 추가
files:write             파일 업로드
threads:read            스레드 읽기
commands                슬래시 명령어
app_mentions:read       @claude 멘션
```

### 3. 슬래시 명령어 등록 (Slack API)
1. Slack API → "Slash Commands"
2. 다음 명령어 추가:
   - `/slack` — Claude 명령 실행
   - `/analyze` — 파일 분석
   - `/report` — 리포트 생성

---

## 🚀 사용 워크플로우

### 시나리오 1: rule-developer 결과 공유

```
1️⃣ 사용자 (Slack)
   /slack run rule-developer를 분석해줘: https://github.com/...

2️⃣ Claude Code
   ├─ rule-developer 하네스 실행
   ├─ Phase 1-4 완료
   └─ 결과 파일 생성

3️⃣ Slack 자동 공유
   #rule-developer 채널:
   ✅ 분석 완료
   📄 code-analysis-report.md
   📄 rule-design-spec.md
   📄 developer-agent-spec.md
   📄 validation-report.md
   
   🔗 다운로드 가능
```

### 시나리오 2: GitHub 이벤트 자동 공유

```
1️⃣ GitHub에서 PR 열림

2️⃣ Slack 자동 알림 (#github-events)
   🔔 PR #123: "Add feature"
   작성자: @user123
   링크: [GitHub PR 보기]

3️⃣ Slack에서 /slack 명령으로 리뷰
   /slack review PR #123
   
4️⃣ Claude가 PR 분석 후 스레드로 응답
```

### 시나리오 3: 코드 분석 요청

```
1️⃣ JellyPlayer.cs 파일 Slack에 업로드
   메시지: "이거 성능 분석해줄래?"

2️⃣ Claude가 파일 자동 감지

3️⃣ 분석 후 스레드로 응답:
   ✅ 성능 이슈 분석 완료
   
   🔴 문제점:
   - Update()에서 매 프레임마다 Find() 호출
   - Physics.OverlapSphere 오버헤드
   
   ✅ 개선안:
   - 캐싱 추가
   - 업데이트 빈도 조절

4️⃣ Slack 스레드에서 계속 대화
```

---

## 🛠️ 설정 옵션

```json
{
  "slack": {
    "enabled": true,
    "bot_token": "xoxb-...",
    
    "channels": {
      "default": "#claude-code",
      "notifications": "#claude-notifications",
      "github": "#github-events",
      "rule_developer": "#rule-developer"
    },
    
    "features": {
      "auto_notifications": true,
      "github_integration": true,
      "rule_developer_share": true,
      "file_analysis": true,
      "slash_commands": true,
      "mention_response": true,
      "thread_management": true
    },
    
    "message_format": "markdown",
    "thread_replies": true,
    "file_uploads": true,
    "reactions": true
  }
}
```

---

## 📌 주의사항

### 보안
- Bot Token을 `.env` 또는 보안 저장소에 저장
- 공개 저장소에 Token 노출 금지
- 정기적으로 Token 로테이션

### 성능
- 대용량 파일은 자동 분할 전송
- 동시 메시지 전송 제한 (API Rate Limit)
- 스레드는 500개 메시지까지 관리

### 알림
- 불필요한 알림은 채널 선택으로 필터링
- 작업 완료 알림은 기본 활성화
- 중요도별로 반응(:white_check_mark:, :warning:, :x:) 표시

---

## 📊 메시지 템플릿

### 작업 완료 알림
```
✅ 작업 완료
작업명: {task_name}
파일: {output_files}
소요시간: {duration}
상태: {status}
```

### GitHub 이벤트
```
{event_type}
저장소: {repo}
작성자: {author}
제목: {title}
링크: {github_url}
```

### rule-developer 결과
```
📋 규칙 분석 완료
저장소: {repo_url}
분석 대상: {analysis_type}

✅ 생성된 파일:
- code-analysis-report.md
- rule-design-spec.md
- developer-agent-spec.md
- validation-report.md

📊 검증 결과:
🟢 Critical: {count}
🟡 Major: {count}
🔵 Minor: {count}
```

---

## ✅ 체크리스트

- [ ] Slack App 생성 (api.slack.com)
- [ ] Bot Token 발급
- [ ] 필요한 Scopes 추가
- [ ] Bot를 Workspace에 설치
- [ ] Slack 채널 생성 (#claude-code 등)
- [ ] .claude/settings.json에 설정 추가
- [ ] 슬래시 명령어 등록 (선택)
- [ ] /slack 명령어 테스트
- [ ] 알림 테스트
- [ ] 문서 팀에 공유

---

**생성:** 2026-04-21  
**상태:** ✅ 전체 기능 Slack 스킬 정의 완료
