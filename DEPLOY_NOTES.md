# Speak Tactics — 배포·운영 정리

마지막 정리: 2026-10-28

## 1. 주소

| 용도 | 주소 | 언제 쓰나 |
|---|---|---|
| **앱 (배포된 사이트)** | https://deplo0504.github.io/speak-tactics/ | 평소 영어 회화할 때. 아이폰 홈 화면 추가·북마크는 이 주소로 |
| 저장소 (코드 보관함, 공개) | https://github.com/deplo0504/speak-tactics | 코드와 수정 기록을 볼 때. 평소엔 열 일 없음 |
| 로컬 개발 서버 | http://localhost:8765 | 맥에서 수정한 걸 배포 전에 시험할 때 (아래 4번으로 켜야 열림) |

- 저장소에 새 코드가 올라가면 GitHub Pages가 1~2분 안에 앱 주소에 자동 반영한다.
- 주소 규칙: `github.com/계정/저장소` = 코드 보관함, `계정.github.io/저장소` = 그 저장소로 만든 웹사이트.
- 앱 주소는 HTTPS라서 아이폰 Safari에서도 마이크가 동작한다. localhost 주소는 맥에서만 열린다.

## 2. 새 주소(또는 새 기기)에서 처음 쓸 때

API 키와 기록은 **주소·기기·브라우저마다 따로** 저장된다. 그래서 새로 열면 빈 상태로 시작한다.

1. ⚙️ 설정 → Gemini API 키 입력 → **💾 키 저장 + 모델 조회**
   - 키는 그 브라우저의 `localStorage['rpg_secrets']`에만 저장되고 저장소에는 절대 올라가지 않는다.
2. 기존 기록(EXP·도감·내 토픽·세션) 옮기기
   - 원래 쓰던 곳: 📊 기록 → **📤 세이브 코드 복사**
   - 새 곳: 📊 기록 → **📥 불러오기** → 붙여넣기 → 합치기 또는 덮어쓰기
   - 세이브 코드에는 API 키가 포함되지 않는다. 모델 설정은 같이 따라온다.
3. 권장 설정 (5번 참고)

## 3. 코드 수정 → 배포

- 수정은 `index.html` 하나만 고치면 된다 (빌드 과정 없음).
- 배포 = 커밋 후 `main` 브랜치에 push. 1~2분 뒤 앱 주소에 반영된다.

```bash
cd ~/Documents/english_speaking_rpg_claude
git add index.html
git commit -m "변경 내용"
git -c credential.helper= -c "credential.helper=!$HOME/.local/bin/gh auth git-credential" push
```

- 반영이 안 보이면 브라우저 새로고침. 그래도 옛 화면이면 크롬은 Cmd+Shift+R(강력 새로고침).
  - Safari에서는 Cmd+Shift+R이 읽기 모드라 새로고침이 아니다. Safari 강력 새로고침은 Cmd+Option+R.
- 배포 상태 확인:

```bash
~/.local/bin/gh api repos/deplo0504/speak-tactics/pages/builds/latest --jq .status
```

`built`면 완료, `building`이면 진행 중, `errored`면 실패.

## 4. 로컬 개발 서버 켜기

맥 터미널에서:

```bash
cd ~/Documents/english_speaking_rpg_claude
python3 -m http.server 8765 --bind 127.0.0.1
```

- 이후 http://localhost:8765 접속. 끌 때는 터미널에서 Ctrl+C.
- 이 서버는 캐시 금지 헤더를 안 보내서, 파일을 고친 뒤 옛 화면이 보이면 강력 새로고침.

## 5. 권장 설정 (⚙️ 설정)

| 항목 | 값 | 이유 |
|---|---|---|
| 💬 회화·HUD 모델 | `gemini-3.6-flash` | 3.8-flash는 429(한도 초과)·503(과부하)·응답 지연이 잦았음 |
| 🛟 폴백 체인 | `gemini-3.6-flash` 다음 줄에 `gemini-3.5-flash-lite` | 3.8-flash는 빼거나 맨 뒤 |
| 🎧 전사 모델 | `gemini-3.5-flash-lite` (기본값) | 빠름. 전사가 자주 틀리면 `gemini-3.6-flash`로 |
| 🔊 음성 | ⚡ 속도 우선 (기본값) | 빠르게 하려면 📢 브라우저 기본 음성 (TTS 대기 0초, API 한도 안 씀) |
| 🔁 핸즈프리 | 켜짐 (기본값) | AI 말이 끝나면 자동 녹음 |
| 🤫 무음 자동 전송 | 켜짐 (5초) | 말을 멈추고 5초 지나면 내 턴 종료 |

- 설정 화면의 드롭다운에 원하는 모델이 없으면 바로 아래 입력칸에 모델 ID를 직접 쓰고 칸 바깥을 클릭하면 저장된다.

## 6. 대화 화면 사용법 요약

| 동작 | 방법 |
|---|---|
| 내 턴 시작 | 핸즈프리면 자동. 아니면 🎙️ 버튼 또는 **Space** |
| 내 턴 끝내기 | ⏹ 버튼 또는 **Space**, 또는 말 멈추고 5초 |
| AI 음성 건너뛰기 | ⏭ 버튼 또는 Space |
| 잠깐 멈춤 / 재개 | ☕ 잠깐 멈춤 버튼 (재개는 Space로도) |
| 막힐 때 | ⏸️ SOS 표현 (타이머 멈춤, 한국어로 물어보기) |
| 잘못 받아쓴 내 말 | 내 말풍선의 🔁 내 녹음으로 확인 → ✏️ 고치기 또는 🎙️ 다시 말하기 |
| 응답 속도 보기 | 대화 화면 오른쪽 위 시간 배지를 탭하면 최근 10턴 표 |

- 녹음을 켜 두고 30초 동안 아무 말이 없으면 보내지 않고 녹음만 멈춘다.

## 7. API 사용량 바 읽는 법

- 모든 화면 맨 위 줄 아래의 "📊 API" 줄. 탭하면 모델별 상세.
- 구글 API는 남은 한도를 알려주지 않는다. 그래서 **이 기기에서 보낸 요청 수를 센 추정치**다. 같은 키를 다른 기기에서도 쓰면 실제보다 적게 보인다.
- 모델별 하루 한도를 상세 화면에 입력하면 %로 보인다. 비워 두면 하루 한도 초과(429)가 났을 때 그때까지의 요청 수를 한도로 자동 기억한다.
- 하루 한도는 미국 태평양 시간 자정에 초기화된다.
  - 서머타임 중(대략 3월 둘째 주~11월 첫째 주): 한국 시간 **오후 4시**
  - 그 외 기간: 한국 시간 **오후 5시**
- 정확한 한도: https://ai.google.dev/gemini-api/docs/rate-limits 또는 AI Studio의 사용량 화면.

## 8. 이 맥에 설치·설정된 것

- **GitHub CLI**: `~/.local/bin/gh` (v2.103.3, 공식 릴리스, 체크섬 확인 후 설치)
  - `deplo0504` 계정으로 로그인됨. 상태 확인: `~/.local/bin/gh auth status`
  - 다시 로그인: `~/.local/bin/gh auth login --hostname github.com --git-protocol ssh --skip-ssh-key --web`
  - 로그아웃: `~/.local/bin/gh auth logout`
- **이 저장소의 커밋 작성자** (이 폴더에만 적용, 전역 설정 아님)
  - 이름: `deplo0504`
  - 이메일: `349110323+deplo0504@users.noreply.github.com` (GitHub 비공개 주소, Gmail 노출 방지)

## 9. 브라우저 저장 데이터 (localStorage 키)

| 키 | 내용 | 세이브 코드에 포함 |
|---|---|---|
| `rpg_secrets` | Gemini API 키 | ❌ 절대 포함 안 됨 |
| `rpg_state` | EXP·설정·토픽 진행·도감·세션 기록 | ✅ |
| `rpg_pending_report` | 리포트 생성 전 실패한 세션 (재시도용) | ❌ |
| `rpg_model_cache` | 조회한 모델 목록 | ❌ |
| `rpg_model_caps` | 모델별로 거부된 thinking 설정 기억 | ❌ |
| `rpg_usage`, `rpg_usage_limits` | 오늘 요청 수, 모델별 하루 한도 | ❌ |

## 10. 버전 기록

| 커밋 | 내용 |
|---|---|
| `78917bd` | 1차: 코어 회화 루프 (토픽 36개, HUD, 타이머, SOS, 리포트, 도감) |
| `2db476e` | 응답 시간 계측, 스트리밍, thinking 최소화, TTS 속도/품질 선택, 첫 질문 미리 준비 |
| `445ab5a` | 느린 모델 대응: 4초 헤징, 모델 상태 기억, TTS 문장 분할 |
| `c9c8e85` | 녹음 전사를 대화와 분리 (환각 전사 해결), 내 녹음 듣기·고치기·다시 말하기 |
| `6f5a00b` | 설정 드롭다운이 직접 입력한 모델을 보여 주도록 수정 |
| `5dc5d8e` | 핸즈프리, 무음 5초, Space 단축키, 일시정지, API 사용량 바 |

## 11. 남은 계획 (원래 3단계 로드맵)

- 2차: 레벨·계급·아바타 진화, 5대 스탯 레이더 차트, 진급 보스전, 도감 블라인드 퀴즈, AI 신규 토픽 생성
- 3차: Gemini Live 실시간 음성 모드, Firebase 클라우드 동기화, 사용법 가이드, PWA 마감
