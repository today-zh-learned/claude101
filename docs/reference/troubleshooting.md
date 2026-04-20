---
title: "문제 해결"
---
# 문제 해결

> 자주 발생하는 문제와 해결 방법을 모았습니다.

Claude Code를 쓰다 보면 크고 작은 문제를 만날 수 있습니다. 대부분은 여기 있는 방법으로 해결됩니다. **증상 → 원인 → 해결법** 순서로 정리했습니다.

---

## 설치 문제

### Node.js가 없다는 에러

**증상**: `npm: command not found` 또는 `node: command not found`

**원인**: Node.js가 설치되어 있지 않거나, 버전이 너무 낮습니다. Claude Code는 Node.js 18 이상이 필요합니다.

**해결법**:

`# Node.js 버전 확인
node --version
 
# 설치되어 있지 않다면
# macOS (Homebrew)
brew install node
 
# Windows (공식 사이트)
# https://nodejs.org 에서 LTS 버전 다운로드
 
# 버전이 낮다면
# nvm으로 관리
nvm install 20
nvm use 20`
Node.js 설치 후에도 `command not found`가 나오면, 터미널을 완전히 종료했다가 다시 열어보세요.

### 권한 오류 (EACCES)

**증상**: `npm install -g` 실행 시 `EACCES: permission denied`

**원인**: 글로벌 npm 디렉토리에 대한 쓰기 권한이 없습니다.

**해결법**:

`# 방법 1: npm 글로벌 경로 변경 (추천)
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
 
# 방법 2: nvm 사용 (가장 추천)
# nvm은 사용자 디렉토리에 설치하므로 권한 문제 없음
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
 
# 방법 3: sudo 사용 (비추천이지만 급할 때)
sudo npm install -g @anthropic-ai/claude-code`
### PATH 문제

**증상**: `claude: command not found` (설치는 됐는데 실행이 안 됨)

**원인**: Claude Code 실행 파일 경로가 시스템 PATH에 없습니다.

**해결법**:

`# Claude Code가 어디에 설치됐는지 확인
npm list -g @anthropic-ai/claude-code
which claude
 
# PATH에 추가
# npm 글로벌 bin 경로 확인
npm config get prefix
# 결과 예: /usr/local → /usr/local/bin이 PATH에 있어야 함
 
# .zshrc (macOS) 또는 .bashrc (Linux)에 추가
echo 'export PATH="$(npm config get prefix)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc`
---

## API 키 문제

### API 키 인증 에러

**증상**: `Invalid API key` 또는 `Authentication error`

**원인**: API 키가 잘못 입력됐거나, 만료됐거나, 형식이 틀렸습니다.

**해결법**:

`# 현재 설정된 키 확인 (앞부분만 표시됨)
echo $ANTHROPIC_API_KEY
 
# 키 재설정
export ANTHROPIC_API_KEY="sk-ant-api03-실제키값"
 
# 영구 설정 (.zshrc에 추가)
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-실제키값"' >> ~/.zshrc
source ~/.zshrc
 
# 또는 Claude Code 내에서 로그인
claude /login`
API 키는 `sk-ant-api03-`으로 시작합니다. 키 앞뒤에 공백이나 따옴표가 잘못 들어가지 않았는지 확인하세요.

### 잔액 부족 (Rate Limit)

**증상**: `Rate limit exceeded` 또는 `429 Too Many Requests` 또는 `Insufficient credits`

**원인**: API 사용량이 한도를 초과했거나, 크레딧이 부족합니다.

**해결법**:

1. **Anthropic Console** ([https://console.anthropic.com)에서](https://console.anthropic.com)%EC%97%90%EC%84%9C) 사용량 확인

1. 크레딧 충전 또는 플랜 업그레이드

1. 당장 급하다면 잠시 기다린 후 재시도 (보통 1분)

1. `/cost` 명령어로 현재 세션 비용 확인

`# 세션 내에서 비용 확인
/cost
 
# 더 저렴한 모델로 전환
/model claude-haiku-3.5-20241022`
### API 키가 노출됐을 때

**증상**: API 키가 Git에 커밋됐거나, 공개 저장소에 올라감

**원인**: `.env` 파일이 `.gitignore`에 없었거나, 코드에 직접 키를 적었습니다.

**해결법**:

`# 1. 즉시 키 비활성화
# Anthropic Console → API Keys → 해당 키 삭제
 
# 2. 새 키 발급
 
# 3. Git 기록에서 제거
# (이미 push했다면 키를 삭제하는 게 가장 안전)
 
# 4. .gitignore에 추가
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
 
# 5. 환경변수로 관리
# 코드에 직접 키를 적지 마세요`
키가 공개 저장소에 올라갔다면, **즉시 해당 키를 삭제**하고 새로 발급받으세요. Git 기록에서 지워도 이미 노출된 키는 안전하지 않습니다.

---

## 실행 문제

### 응답이 너무 느림

**증상**: Claude의 응답이 30초 이상 걸리거나, 진행이 멈춘 것처럼 보임

**원인**: 프로젝트 규모가 크거나, 복잡한 작업을 처리 중이거나, 네트워크가 느립니다.

**해결법**:

1. **작업을 작게 나누기** — “전체 리팩토링해줘” 대신 “이 함수만 리팩토링해줘”

1. **파일 지정하기** — 구체적인 파일 경로를 알려주면 검색 시간 단축

1. **모델 변경** — `/model claude-haiku-3.5-20241022`로 가벼운 모델 사용

1. **네트워크 확인** — VPN 사용 중이라면 끊어보기

1. `Ctrl + C`로 중단 후 더 구체적으로 다시 요청

### 컨텍스트 초과

**증상**: `Context window limit reached` 또는 대화가 갑자기 이상해짐

**원인**: 대화가 너무 길어져서 Claude가 기억할 수 있는 범위를 초과했습니다.

**해결법**:

`# 방법 1: 대화 압축 (추천)
/compact
 
# 방법 2: 커스텀 지시로 압축
/compact 코드 변경사항 위주로 요약해줘
 
# 방법 3: 새 세션 시작
/clear
 
# 방법 4: 중요한 내용은 CLAUDE.md에 기록
# 대화가 초기화돼도 CLAUDE.md는 남아있으므로`
`/compact`는 대화 내용을 요약해서 컨텍스트를 확보합니다. 작업 중간에 수시로 사용하면 좋습니다.

### 무한 루프

**증상**: Claude가 같은 작업을 반복하거나, 파일을 계속 수정했다 되돌림

**원인**: 지시가 모호하거나, 상충되는 조건이 있거나, 린트/테스트 규칙이 충돌합니다.

**해결법**:

1. `Esc` 두 번 눌러서 즉시 중단

1. 무엇을 반복하고 있는지 확인

1. 더 구체적으로 재지시

`# 나쁜 예 (무한 루프 유발)
"에러 없을 때까지 고쳐줘"

# 좋은 예
"이 에러를 고쳐줘: [구체적 에러]. 3번 시도해서 안 되면 멈추고 알려줘"`
### 권한 거부 (Permission Denied)

**증상**: Claude가 파일 수정이나 명령 실행을 거부함

**원인**: 기본 모드에서는 파일 쓰기와 명령 실행 시 확인을 요청합니다.

**해결법**:

`# 방법 1: 허용 프롬프트에서 y 입력
# [Y/n] 프롬프트가 나오면 y 누르기
 
# 방법 2: 특정 도구 자동 허용
claude --allowedTools "Write,Edit,Bash(npm test)"
 
# 방법 3: 프로젝트 설정에서 허용
# .claude/settings.json
{
  "permissions": {
    "allow": ["Write", "Edit", "Bash(npm *)"]
  }
}`
---

## MCP 문제

### MCP 서버 연결 실패

**증상**: `MCP server failed to start` 또는 `Connection refused`

**원인**: MCP 서버 실행 파일을 찾지 못하거나, 서버에 버그가 있습니다.

**해결법**:

`# 1. MCP 상태 확인
/mcp
 
# 2. MCP 설정 확인
cat .claude/mcp.json
 
# 3. 서버가 단독으로 실행되는지 테스트
npx @anthropic-ai/mcp-server-명 --help
 
# 4. 서버 재등록
claude mcp remove server-name
claude mcp add server-name npx @anthropic-ai/mcp-server-명
 
# 5. 로그 확인
# Claude Code 실행 시 터미널에 MCP 관련 에러 메시지 확인`
### MCP 타임아웃

**증상**: `MCP request timed out` 또는 MCP 도구 호출 후 응답 없음

**원인**: MCP 서버가 너무 오래 걸리는 작업을 수행 중이거나, 멈췄습니다.

**해결법**:

1. `Esc` 두 번으로 현재 작업 중단

1. `/mcp`로 서버 상태 확인

1. 필요하면 세션 재시작 (`/clear` 또는 `Ctrl + D` 후 재실행)

1. MCP 서버 설정에서 타임아웃 값 조정

`// .claude/mcp.json 예시
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-명"],
      "timeout": 30000
    }
  }
}`
### MCP 설정 오류

**증상**: MCP 서버가 목록에 없거나, 설정이 무시됨

**원인**: JSON 문법 오류이거나, 설정 파일 경로가 잘못됐습니다.

**해결법**:

`# 1. JSON 문법 검사
cat .claude/mcp.json | python3 -m json.tool
 
# 2. 올바른 위치에 파일이 있는지 확인
ls -la .claude/mcp.json          # 프로젝트 레벨
ls -la ~/.claude/mcp.json         # 전역 레벨
 
# 3. claude mcp 명령어로 재설정 (수동 편집보다 안전)
claude mcp add server-name npx @anthropic-ai/mcp-server-명
 
# 4. 환경변수가 필요한 서버라면
claude mcp add server-name -e API_KEY=값 npx @anthropic-ai/mcp-server-명`
---

## Git 문제

### 커밋 실패

**증상**: Claude가 커밋하려는데 `pre-commit hook failed` 또는 커밋이 안 됨

**원인**: pre-commit 훅(린트, 포매터 등)이 에러를 발견했습니다.

**해결법**:

`# Claude에게 문제 해결 요청
"커밋이 실패했어. 린트 에러 고치고 다시 커밋해줘"
 
# 또는 직접 확인
npx lint-staged  # pre-commit이 lint-staged를 쓴다면
 
# 훅 자체가 문제라면 (비추천)
# git commit --no-verify
# 이 방법은 Claude Code에서 기본적으로 사용하지 않습니다`
### PR 생성 오류

**증상**: PR을 만들려는데 `gh: command not found` 또는 인증 오류

**원인**: GitHub CLI(gh)가 설치되지 않았거나, 인증이 안 됐습니다.

**해결법**:

`# gh 설치
brew install gh  # macOS
# 또는 https://cli.github.com
 
# gh 인증
gh auth login
# 브라우저가 열리면 GitHub에 로그인
 
# 인증 상태 확인
gh auth status`
---

## 그래도 안 될 때

위 방법으로도 해결이 안 되면 다음을 시도해보세요.

1. **Claude Code 업데이트**: `claude update`

1. **환경 진단 실행**: `/doctor`

1. **캐시 초기화**: `rm -rf ~/.claude/cache`

1. **재설치**: `npm uninstall -g @anthropic-ai/claude-code && npm install -g @anthropic-ai/claude-code`

1. **공식 문서 확인**: [https://docs.anthropic.com/en/docs/claude-code](https://docs.anthropic.com/en/docs/claude-code)

1. **GitHub Issues 검색**: [https://github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues)

재설치해도 CLAUDE.md나 프로젝트 설정(.claude/ 폴더)은 그대로 남아있으니 걱정하지 마세요.
