# Unity 표준 폴더 구조 템플릿 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | folder-structure-template |
| 역할 | Unity 프로젝트 표준 폴더 구조 및 99_ExternalAsset 하위 구조 정의 |
| 사용 에이전트 | file-creator, file-structure-manager |
| 참조 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |

---

## Unity 표준 폴더 구조

### 전체 폴더 트리

```
Assets/
├── 00_Core/                          [핵심 게임 시스템]
│   ├── Architecture/                 [게임 아키텍처 (매니저, 싱글톤)]
│   ├── Config/                       [게임 설정 (Scriptable Objects)]
│   ├── Constants/                    [상수 정의 (GameConsts.cs, Tags 등)]
│   └── Utils/                        [유틸리티 함수 (Helper, Extensions)]
│
├── 01_Scenes/                        [씬 파일]
│   ├── Levels/                       [스테이지/레벨 씬]
│   ├── UI/                           [UI 씬 (메뉴, 로딩 등)]
│   └── Temp/                         [테스트용 임시 씬]
│
├── 02_Scripts/                       [게임 로직 스크립트]
│   ├── Gameplay/                     [게임플레이 로직]
│   │   ├── Player/                   [플레이어 관련]
│   │   ├── Enemy/                    [적 관련]
│   │   ├── Items/                    [아이템 관련]
│   │   └── World/                    [월드/환경 관련]
│   ├── UI/                           [UI 로직]
│   ├── Services/                     [게임 서비스 (인풋, 사운드, 분석 등)]
│   └── Events/                       [이벤트 시스템]
│
├── 03_Art/                           [미술 자산]
│   ├── Sprites/                      [2D 스프라이트]
│   │   ├── Characters/
│   │   ├── UI/
│   │   ├── Environment/
│   │   └── Effects/
│   ├── Models/                       [3D 모델]
│   ├── Materials/                    [머티리얼]
│   ├── Prefabs/                      [프리팹 (2D/3D)]
│   │   ├── Characters/
│   │   ├── UI/
│   │   ├── Environment/
│   │   └── Effects/
│   └── Animations/                   [애니메이션 (.anim, AnimatorController)]
│
├── 04_Audio/                         [오디오 자산]
│   ├── SFX/                          [효과음]
│   ├── BGM/                          [배경음]
│   └── Voice/                        [음성]
│
├── 05_Data/                          [데이터 자산]
│   ├── Configs/                      [JSON, ScriptableObject 설정]
│   ├── Localization/                 [다국어 데이터]
│   └── SaveData/                     [저장 데이터 (런타임)]
│
├── 06_UI/                            [UI 프리팹 및 레이아웃]
│   ├── Screens/                      [전체 화면 UI]
│   ├── Panels/                       [팝업, 패널]
│   ├── Widgets/                      [재사용 가능 위젯]
│   └── Themes/                       [UI 테마 (폰트, 색상 등)]
│
├── 07_Resources/                     [런타임 동적 로드 자산]
│   └── [Resources 폴더로 제한적 사용]
│
├── 08_Editor/                        [에디터 전용 스크립트 및 도구]
│   ├── Tools/                        [편집기 도구]
│   ├── Windows/                      [에디터 윈도우]
│   └── Inspectors/                   [커스텀 Inspector]
│
├── 09_Tests/                         [테스트 코드]
│   ├── PlayMode/                     [Play Mode 테스트]
│   └── EditMode/                     [Edit Mode 테스트]
│
├── 10_Shaders/                       [쉐이더]
│   └── [.shader, .shadergraph 파일]
│
├── 11_Documentation/                 [문서 (마크다운, 다이어그램)]
│   ├── Architecture/                 [아키텍처 문서]
│   ├── API/                          [API 문서]
│   └── Tutorials/                    [튜토리얼]
│
├── 99_ExternalAsset/                 [외부 라이브러리 및 에셋]
│   ├── AssetStore/                   [Unity Asset Store 구매 에셋]
│   ├── Libraries/                    [제3자 라이브러리 (DLL, JAR 등)]
│   ├── OpenSource/                   [오픈소스 라이브러리]
│   ├── Plugins/                      [네이티브 플러그인 (Android, iOS)]
│   └── README.md                     [외부 자산 관리 가이드]
│
├── Packages/                         [UPM 패키지]
├── ProjectSettings/                  [프로젝트 설정]
└── .gitignore                        [Git 무시 목록]
```

---

## 각 폴더별 용도 설명

### 00_Core
- **목적**: 게임 전체에서 공통으로 사용하는 핵심 시스템
- **포함 파일**: GameManager, PlayerPrefs, 싱글톤, Scriptable Object 설정
- **명명**: `{기능}Manager.cs`, `{기능}Config.cs`

### 01_Scenes
- **목적**: 씬 파일(.unity) 저장
- **규칙**: 씬명 = 폴더명. 예: `Levels/Level_01.unity`
- **메타 파일**: 모든 씬 파일과 폴더마다 `.meta` 생성 필수

### 02_Scripts
- **목적**: 게임 로직 스크립트 전부
- **규칙**: 네임스페이스 = 폴더 경로와 동일
  - `Assets/02_Scripts/Gameplay/Player/PlayerController.cs` → `namespace JellyMooly.Gameplay.Player`

### 03_Art
- **목적**: 2D/3D 미술 자산 및 애니메이션
- **스프라이트**: PNG/PSD (임포트 설정: Filter Mode = Point, Compression = None)
- **모델**: FBX (Import Settings: Rig, Animation 분리)
- **프리팹**: 코드 + 게임오브젝트 조합

### 04_Audio
- **목적**: 모든 오디오 파일
- **포맷**: MP3 (BGM), WAV (SFX)
- **명명**: `{타입}_{기능}_{변형}.{확장자}` 예: `SFX_Jump_01.wav`

### 05_Data
- **목적**: 게임 데이터 및 설정
- **포맷**: JSON (직렬화), ScriptableObject (에디터 설정)
- **로컬라이제이션**: 언어별 폴더 분리 (ko, en, ja, etc)

### 06_UI
- **목적**: UI 프리팹 및 레이아웃 계층구조
- **명명**: `{화면명}_{요소명}.prefab` 예: `MainMenu_StartButton.prefab`

### 07_Resources
- **목적**: 런타임 동적 로드 필요 자산만 저장 (최소한으로)
- **경고**: Resources 폴더는 메모리 효율이 낮으므로 제한적 사용

### 08_Editor
- **목적**: 개발 편의 도구 (플레이 모드에서 불필요)
- **조건**: `#if UNITY_EDITOR` 전처리 지시문 필수
- **명명**: `{기능}EditorWindow.cs`, `{기능}Editor.cs`

### 09_Tests
- **목적**: 유닛 테스트, 통합 테스트
- **패키지**: `com.unity.test-framework` 의존
- **명명**: `{테스트대상}Tests.cs`

### 10_Shaders
- **목적**: 커스텀 쉐이더
- **포맷**: HLSL (.shader), Shader Graph (.shadergraph)
- **명명**: `{용도}_{이름}.shader` 예: `Sprite_Outline.shader`

### 11_Documentation
- **목적**: 기술 문서
- **형식**: Markdown, PlantUML 다이어그램
- **예**: 아키텍처 결정, API 가이드, 개발 튜토리얼

### 99_ExternalAsset
- **목적**: 모든 외부 자산 격리
- **구성**:
  - **AssetStore**: Unity Asset Store에서 구매한 에셋
  - **Libraries**: 서드파티 DLL, JAR, SO 등
  - **OpenSource**: GitHub 등에서 받은 오픈소스 라이브러리
  - **Plugins**: 네이티브 플러그인 (Android: `.aar`, iOS: `.framework`)
- **메타 파일**: 모든 파일과 폴더마다 `.meta` 필수
- **README.md**: 각 하위폴더별 설명과 출처 명시

---

## 생성 규칙 및 네이밍 컨벤션

### 폴더명 규칙

| 타입 | 형식 | 예 | 설명 |
|------|------|-----|------|
| 카테고리 폴더 | `{숫자2자리}_{기능}` | `02_Scripts`, `03_Art` | 정렬 순서 명시 |
| 기능 폴더 | `{기능명}PascalCase` | `Gameplay`, `PlayerController` | 폴더명은 동사 또는 명사 |
| 임시 폴더 | `_Temp`, `_Backup` | `_Temp/ExperimentalFeature` | 언더스코어 접두사 |
| 비활성 폴더 | `~{폴더명}` | `~ObsoleteScripts` | 물결 기호 접두사 |

### 파일명 규칙

| 타입 | 형식 | 예 |
|------|------|-----|
| C# 스크립트 | `{클래스명}.cs` | `PlayerController.cs` |
| ScriptableObject | `{기능}Config.asset` | `GameConfig.asset` |
| 프리팹 | `{타입}_{이름}.prefab` | `Prefab_Player.prefab` |
| 씬 | `{레벨명}.unity` | `Level_01.unity` |
| 머티리얼 | `Mat_{이름}.mat` | `Mat_PlayerSkin.mat` |
| 애니메이션 | `Anim_{행동}.anim` | `Anim_PlayerJump.anim` |
| AnimatorController | `AC_{캐릭터}.controller` | `AC_Player.controller` |

### 네임스페이스 규칙

```csharp
// Assets/02_Scripts/Gameplay/Player/PlayerController.cs
namespace JellyMooly.Gameplay.Player
{
    public class PlayerController : MonoBehaviour { }
}

// Assets/02_Scripts/UI/MainMenu/StartButton.cs
namespace JellyMooly.UI.MainMenu
{
    public class StartButton : MonoBehaviour { }
}
```

---

## 메타 파일 관리 정보

### .meta 파일 규칙

| 항목 | 규칙 | 설명 |
|------|------|------|
| **생성** | 자동 | Unity가 폴더/파일 생성 시 자동 생성 |
| **GUID** | 고유값 | 각 .meta 파일의 guid는 재지정 금지 |
| **커밋** | 필수 | Git에 반드시 커밋 (resource ID 유지) |
| **삭제** | 폴더와 함께 | 폴더 삭제 시 해당 .meta도 함께 삭제 |
| **이동** | 폴더와 함께 | 폴더 이동 시 .meta도 함께 이동 (Unity 담당) |

### 외부 자산 메타 관리

```markdown
## 99_ExternalAsset 메타 정책

- **AssetStore/**: 상업 라이선스 자산 (수정 금지)
  - .meta: 커밋 필수, 영원히 유지

- **OpenSource/**: 오픈소스 라이브러리 (라이선스 준수)
  - .meta: 커밋 필수
  - README.md: 라이선스 표기 필수

- **Libraries/**: 컴파일된 라이브러리
  - .meta: 커밋 필수

- **Plugins/**: 네이티브 플러그인
  - 플랫폼별 .meta 관리 (Android, iOS 분리)
```

### .gitignore 추천 규칙

```gitignore
# 메타 파일은 커밋
# *.meta 는 gitignore에 추가하지 않을 것

# 임시 폴더
_Temp/
~*/

# Library는 gitignore
Library/
Temp/
obj/
Logs/

# 사용자별 설정
.vscode/
.idea/
*.sln.DotSettings.user
```

---

## 폴더 생성 체크리스트

### 신규 프로젝트 초기화 체크리스트

- [ ] `00_Core`, `01_Scenes`, ..., `99_ExternalAsset` 폴더 생성
- [ ] 각 폴더마다 `.gitkeep` 또는 `README.md` 생성
- [ ] 99_ExternalAsset 하위 4개 폴더 생성 (AssetStore, Libraries, OpenSource, Plugins)
- [ ] 모든 폴더의 `.meta` 파일 생성 확인
- [ ] .gitignore 파일 설정
- [ ] README.md 작성 (프로젝트 설정 요약)

### 폴더 추가 시 체크리스트

- [ ] 상위 폴더 규칙 확인 (02_Scripts 내인가, 03_Art 내인가?)
- [ ] 폴더명 규칙 확인 (PascalCase, 특수문자 없음)
- [ ] `.meta` 파일 자동 생성 확인
- [ ] Git 커밋 (폴더 + .meta)

---

## 예제: 새 기능 폴더 추가

### 시나리오: 새로운 게임플레이 시스템 "PowerUp" 추가

```
추가할 폴더 구조:
Assets/02_Scripts/Gameplay/PowerUp/
  ├── PowerUpManager.cs
  ├── PowerUpConfig.cs
  └── PowerUpEffect.cs

Assets/03_Art/Prefabs/PowerUp/
  ├── Prefab_PowerUp_Shield.prefab
  └── Prefab_PowerUp_Speed.prefab

Assets/05_Data/Configs/
  └── PowerUpConfig.asset

추가할 네임스페이스:
namespace JellyMooly.Gameplay.PowerUp
```

### 생성 단계

1. `02_Scripts/Gameplay/PowerUp/` 폴더 생성 (자동으로 `.meta` 생성)
2. C# 스크립트 파일 생성
3. `03_Art/Prefabs/PowerUp/` 폴더 생성
4. 프리팹 파일 생성
5. `05_Data/Configs/PowerUpConfig.asset` 생성
6. 모든 파일 + `.meta` 파일 커밋

---

생성: 2026-05-01
수정 이력: 초안 작성
