---
title: "치트시트"
---
# 치트시트

> 한 페이지에서 모든 핵심 명령어와 단축키를 찾아보세요.

이 페이지는 Claude Code를 쓰다가 “이거 어떻게 하더라?” 싶을 때 빠르게 찾아보는 용도입니다. 북마크 해두시면 편합니다.

---

## CLI 실행 옵션

| 명령어 | 설명 | 예시 |
| --- | --- | --- |
| `claude` | 대화형 세션 시작 | `claude` |
| `claude "메시지"` | 초기 메시지와 함께 시작 | `claude "이 프로젝트 설명해줘"` |
| `claude -p "메시지"` | 한 번만 질문하고 종료 | `claude -p "package.json 요약해줘"` |
| `claude -p "메시지" --output-format json` | JSON 형식으로 출력 | 스크립트 연동 시 유용 |
| `claude --resume` | 마지막 대화 이어하기 | `claude --resume` |
| `claude --resume SESSION_ID` | 특정 세션 이어하기 | `claude --resume abc123` |
| `claude --model MODEL` | 모델 지정 실행 | `claude --model claude-sonnet-4-20250514` |
| `claude --add-dir PATH` | 추가 디렉토리 포함 | `claude --add-dir ../shared-lib` |
| `claude config` | 설정 관리 | `claude config set theme dark` |
| `claude mcp` | MCP 서버 관리 | `claude mcp add server-name` |
| `claude update` | 최신 버전 업데이트 | `claude update` |

---

## 슬래시 커맨드

세션 안에서 `/`로 시작하는 특수 명령어입니다.

| 커맨드 | 설명 | 예시 |
| --- | --- | --- |
| `/help` | 도움말 보기 | `/help` |
| `/clear` | 대화 내역 초기화 | `/clear` |
| `/compact` | 대화 요약 후 압축 | `/compact` |
| `/compact [지시]` | 커스텀 지시로 압축 | `/compact 코드 변경 중심으로` |
| `/config` | 설정 열기 | `/config` |
| `/cost` | 현재 세션 비용 확인 | `/cost` |
| `/doctor` | 환경 진단 | `/doctor` |
| `/init` | CLAUDE.md 생성 | `/init` |
| `/login` | 계정 로그인 | `/login` |
| `/logout` | 계정 로그아웃 | `/logout` |
| `/mcp` | MCP 서버 상태 확인 | `/mcp` |
| `/memory` | CLAUDE.md 편집 | `/memory` |
| `/model` | 모델 변경 | `/model claude-sonnet-4-20250514` |
| `/permissions` | 권한 설정 확인/수정 | `/permissions` |
| `/pr-review` | PR 리뷰 실행 | `/pr-review` |
| `/review` | 코드 리뷰 요청 | `/review` |
| `/status` | 현재 상태 확인 | `/status` |
| `/terminal-setup` | 터미널 Shift+Enter 설정 | `/terminal-setup` |
| `/vim` | Vim 모드 토글 | `/vim` |

`/compact`는 대화가 길어졌을 때 가장 유용합니다. 컨텍스트 초과 경고가 뜨면 바로 써보세요.

---

## 키보드 단축키

| 단축키 | 기능 |
| --- | --- |
| `Enter` | 메시지 전송 |
| `Shift + Enter` | 줄바꿈 (터미널 설정 필요) |
| `Ctrl + C` | 현재 응답 중단 |
| `Ctrl + D` | 세션 종료 |
| `Ctrl + L` | 화면 지우기 |
| `Esc` | 현재 입력 취소 / 편집 중 취소 |
| `Esc` (2회) | 진행 중인 도구 실행 중단 |
| `Tab` | 파일명 자동완성 |
| `↑` / `↓` | 이전/다음 입력 기록 탐색 |

---

## 권한 모드

| 모드 | 파일 읽기 | 파일 쓰기 | 명령 실행 | MCP 도구 | 사용 상황 |
| --- | --- | --- | --- | --- | --- |
| **기본 모드** | O | 확인 요청 | 확인 요청 | 확인 요청 | 일상적인 작업 |
| **자동 수락** (`--allowedTools`) | O | 지정 도구만 | 지정 도구만 | 지정 도구만 | 반복 작업 |
| **Yolo 모드** (`--dangerously-skip-permissions`) | O | O | O | O | 완전 자동화, 신뢰 프로젝트 |

Yolo 모드는 모든 작업을 무조건 허용합니다. 중요한 프로젝트에서는 사용하지 마세요.

---

## 모델 비교

| 모델 | 속도 | 지능 | 가격 | 추천 용도 |
| --- | --- | --- | --- | --- |
| **Claude Sonnet 4** | 빠름 | 높음 | 보통 | 일반 코딩, 일상 작업 (기본값) |
| **Claude Opus 4** | 보통 | 매우 높음 | 높음 | 복잡한 아키텍처, 대규모 리팩토링 |
| **Claude Haiku 3.5** | 매우 빠름 | 보통 | 낮음 | 간단한 질문, 빠른 확인 |

`# 모델 변경
claude --model claude-opus-4-20250514
# 또는 세션 중
/model claude-opus-4-20250514`
---

## 주요 환경변수

| 환경변수 | 설명 | 예시 |
| --- | --- | --- |
| `ANTHROPIC_API_KEY` | API 키 | `sk-ant-api03-...` |
| `CLAUDE_CODE_USE_BEDROCK` | AWS Bedrock 사용 | `1` |
| `CLAUDE_CODE_USE_VERTEX` | Google Vertex 사용 | `1` |
| `CLAUDE_MODEL` | 기본 모델 지정 | `claude-sonnet-4-20250514` |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 최대 출력 토큰 | `16384` |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 텔레메트리 비활성화 | `1` |
| `HTTP_PROXY` / `HTTPS_PROXY` | 프록시 설정 | `http://proxy:8080` |
| `CLAUDE_CODE_CONFIG_DIR` | 설정 디렉토리 변경 | `~/.config/claude-code` |

`# .bashrc 또는 .zshrc에 추가
export ANTHROPIC_API_KEY="sk-ant-api03-your-key-here"`
---

## 설정 파일 경로

| 파일 | 경로 | 용도 |
| --- | --- | --- |
| 프로젝트 CLAUDE.md | `./CLAUDE.md` | 프로젝트별 지시사항 |
| 사용자 CLAUDE.md | `~/.claude/CLAUDE.md` | 전역 지시사항 |
| 설정 파일 | `~/.claude/settings.json` | 사용자 설정 |
| 프로젝트 설정 | `.claude/settings.json` | 프로젝트별 설정 |
| MCP 설정 | `.claude/mcp.json` | MCP 서버 (프로젝트) |
| 전역 MCP 설정 | `~/.claude/mcp.json` | MCP 서버 (전역) |
| Hooks 설정 | `.claude/settings.json` 내 | Hooks 이벤트 핸들러 |
| 인증 정보 | `~/.claude/credentials.json` | API 키/토큰 |

---

## Hooks 이벤트

| 이벤트 | 발생 시점 | 활용 예시 |
| --- | --- | --- |
| `PreToolUse` | 도구 실행 전 | 특정 파일 수정 방지, 명령어 필터링 |
| `PostToolUse` | 도구 실행 후 | 린트 자동 실행, 포맷팅 |
| `Notification` | 알림 발생 시 | Slack/Discord 알림 전송 |
| `Stop` | 응답 완료 시 | 요약 로깅, 자동 후처리 |
| `SubagentStop` | 서브에이전트 완료 시 | 서브 작업 모니터링 |

`// .claude/settings.json 내 Hooks 설정 예시
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "npx prettier --write $CLAUDE_FILE_PATH"
      }
    ]
  }
}`
---

## 자주 쓰는 패턴 모음

`# 프로젝트 파악
claude "이 프로젝트 구조 설명해줘"
 
# 파일 만들기
claude "src/utils/format.ts 만들어줘. 날짜 포맷 함수 3개"
 
# 에러 해결
claude "이 에러 고쳐줘: [에러 메시지 붙여넣기]"
 
# Git 작업
claude "변경사항 커밋해줘"
claude "/pr-review"
 
# 리팩토링
claude "이 함수 더 읽기 쉽게 고쳐줘"
 
# 파이프라인
cat error.log | claude -p "이 로그에서 에러 원인 찾아줘"`
이 치트시트의 내용이 기억나지 않을 때는 세션 안에서 `/help`를 입력하면 기본 도움말을 볼 수 있습니다.
