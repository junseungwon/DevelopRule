# Slack 연동 설정 가이드

## 📋 전체 흐름

```
1️⃣ Slack App 생성 & Token 발급 (5분)
         ↓
2️⃣ Bot 권한 설정 (2분)
         ↓
3️⃣ Claude Code 설정 추가 (3분)
         ↓
4️⃣ 테스트 및 확인 (5분)
```

---

## 🔧 Step 1: Slack Bot 생성 및 Token 발급

### 1-1. Slack API에서 App 생성

1. https://api.slack.com/apps 접속
2. **"Create New App"** 버튼 클릭
3. **"From scratch"** 선택

   ![image](https://user-images.githubusercontent.com/...)

4. 다음 정보 입력:
   - **App name**: `Claude Code` (또는 선호하는 이름)
   - **Workspace**: 사용할 Slack Workspace 선택

5. **"Create App"** 클릭

### 1-2. OAuth Token 생성

좌측 메뉴에서 **"OAuth & Permissions"** 클릭

#### Scopes 추가 (Bot Token Scopes)

**"Add an OAuth Scope"**를 클릭하여 다음 권한들을 추가:

```
chat:write              메시지 작성
channels:read           채널 정보 읽기
users:read              사용자 정보 읽기
files:write             파일 업로드
reactions:write         반응(이모지) 추가
threads:read            스레드 읽기
commands                슬래시 명령어
app_mentions:read       @메멘션 감지
```

✅ 이 정도면 충분합니다. 추가 기능은 나중에 추가 가능.

#### Bot 설치

- **"Install to Workspace"** 클릭
- 권한 승인 ("허용" 버튼 클릭)
- **Bot User OAuth Token** 복사 (xoxb-로 시작)

예: `xoxb-1234567890-1234567890-AbCdEfGhIjKlMnOpQrStUvWx`

⚠️ **중요**: 이 Token을 안전하게 보관하세요!

---

## 🔐 Step 2: Claude Code에 Token 설정

### 2-1. 프로젝트의 `.claude/settings.json` 편집

JellyMolly 프로젝트 루트에서:

```bash
# 디렉토리 확인
cd d:\ProjectFiles\JellyMolly\.claude

# settings.json 파일 생성 또는 편집
# (파일이 없으면 생성)
```

### 2-2. Slack 설정 추가

다음 내용을 `.claude/settings.json`에 추가:

```json
{
  "slack": {
    "enabled": true,
    "bot_token": "xoxb-YOUR-BOT-TOKEN-HERE",
    
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
      "mention_response": true
    }
  }
}
```

**Token 교체하기:**

위의 `xoxb-YOUR-BOT-TOKEN-HERE` 부분을 Step 1-2에서 복사한 Bot Token으로 교체:

```json
"bot_token": "xoxb-1234567890-1234567890-AbCdEfGhIjKlMnOpQrStUvWx"
```

### 2-3. 파일 저장

---

## 🎯 Step 3: Slack 채널 준비

### 3-1. 필요한 채널 생성

Slack Workspace에서:

1. **#claude-code** — 일반 명령 및 응답
2. **#claude-notifications** — 작업 완료 알림
3. **#github-events** — GitHub PR/Issue 알림
4. **#rule-developer** — 규칙 분석 결과

### 3-2. Bot 초대

각 채널에 Claude Code Bot을 초대:

```
채널 → 상세 정보 → 멤버 → Claude Code 추가
```

### 3-3. 권한 설정

- Claude Code Bot에게 **메시지 작성 권한** 필요
- 나머지는 기본값 유지

---

## ✅ Step 4: 테스트

### 4-1. 기본 메시지 전송 테스트

**Slack에서:**

```
#claude-code 채널로 이동
메시지 입력: "안녕 Claude!"
```

**Claude Code가 응답해야 함:**

```
안녕하세요! Claude Code입니다.
Slack 연동이 성공적으로 설정되었습니다. ✅
```

### 4-2. /slack 명령어 테스트

```
/slack ask 안녕, 너 뭐하니?
```

Claude가 스레드로 응답하면 성공!

### 4-3. 알림 테스트

```
/slack send-test notification
```

#claude-notifications 채널에 테스트 메시지 도착하면 성공!

---

## 🚀 실제 사용 예시

### 예시 1: rule-developer 분석 결과 공유

```
Slack에서:
/slack run rule-developer를 분석해줘: https://github.com/owner/repo

Claude:
1. GitHub 저장소 분석 시작
2. rule-developer 하네스 실행 (Phase 1-4)
3. 결과를 #rule-developer 채널에 자동 공유
```

결과:
```
#rule-developer 채널:

📋 분석 완료: owner/repo
├─ code-analysis-report.md ✅
├─ rule-design-spec.md ✅
├─ developer-agent-spec.md ✅
└─ validation-report.md ✅

⚠️ Critical: 0 | Major: 1 | Minor: 3
🔗 [결과 다운로드]
```

### 예시 2: 코드 분석 요청

```
Slack에서:
JellyPlayer.cs 파일 업로드
메시지: "@claude 이거 성능 분석해줄래?"

Claude:
파일 자동 감지 → 분석 → 스레드로 응답

결과:
✅ 성능 분석 완료

문제점:
🔴 Update()에서 매 프레임 Find() 호출
   → 캐싱으로 개선 가능

개선안:
✅ Awake()에서 한 번만 초기화
✅ 접근 패턴 최적화
```

### 예시 3: GitHub 이벤트 자동 알림

```
GitHub에서 PR 열림 (#123: "Add feature")
         ↓
Slack 자동 알림 (#github-events):

🔔 PR #123: "Add feature"
작성자: @user123
저장소: owner/repo
[GitHub에서 보기]

Slack에서:
/slack review PR #123

Claude:
PR 분석 후 스레드로 코드 리뷰 제공
```

---

## 🔧 고급 설정

### 기본 채널 변경

`.claude/settings.json`에서:

```json
"channels": {
  "default": "#my-channel"  // 기본 채널 변경
}
```

### 특정 기능만 활성화

```json
"features": {
  "auto_notifications": false,  // 알림 비활성화
  "github_integration": true,   // GitHub 알림만 활성화
  "file_analysis": true
}
```

### GitHub 연동 상세 설정

```json
"github": {
  "repo": "zkdlman1/JellyMolly",
  "events": ["pull_request", "issues"],
  "branches": ["main", "develop"]
}
```

---

## 🆘 문제 해결

### "Bot Token이 유효하지 않습니다" 오류

**원인**: Token이 잘못되었거나 만료됨

**해결**:
1. api.slack.com에서 확인
2. Token 재생성 (OAuth & Permissions)
3. settings.json 다시 업데이트

### Slack에서 메시지를 받지 못함

**원인**: Bot이 채널에 초대되지 않음

**해결**:
1. 채널 → 상세 정보
2. 멤버 추가 → Claude Code Bot 검색 → 추가

### /slack 명령어가 작동하지 않음

**원인**: 슬래시 명령어 등록 필요 (선택사항)

**해결**: 
1. api.slack.com → Slash Commands
2. `/slack` 명령어 추가
3. Request URL: (Claude Code Webhook URL)

---

## 📞 지원

문제가 발생하면:

1. 설정 파일 재확인 (Token, 채널명)
2. Slack App 권한 확인
3. Bot이 채널에 초대되었는지 확인
4. Slack API 문서 참조: https://api.slack.com/docs

---

**설정 완료 후 Claude Code를 재시작하면 Slack 연동이 활성화됩니다!** 🎉
