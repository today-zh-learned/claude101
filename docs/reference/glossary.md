---
title: "용어집"
---
# 용어집

> Claude Code를 쓰면서 만나는 용어들을 모았습니다. 가나다 순으로 정리했으니 필요할 때 찾아보세요.

각 용어 옆의 링크를 누르면 해당 개념을 자세히 배울 수 있는 페이지로 이동합니다.

---

## ㄱ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **권한 모드** | Permission Mode | Claude Code가 파일 수정, 명령 실행 시 허용 여부를 제어하는 설정. 기본/자동 수락/Yolo 세 단계가 있음 | [Day 4]({{ '/docs/day-4/hooks.html' | relative_url }}) |
| **글로벌 설정** | Global Settings | 모든 프로젝트에 공통 적용되는 사용자 설정. `~/.claude/` 디렉토리에 저장됨 | [Day 2]({{ '/docs/day-2/claude-md.html' | relative_url }}) |
| **Glob** | Glob | 파일 이름 패턴(`*.ts`, `src/**/*.jsx` 등)으로 파일을 검색하는 Claude Code 내장 도구 | [Day 1]({{ '/docs/day-1/reading-and-writing.html' | relative_url }}) |
| **Grep** | Grep | 파일 내용에서 텍스트를 검색하는 Claude Code 내장 도구. 정규표현식도 지원 | [Day 1]({{ '/docs/day-1/reading-and-writing.html' | relative_url }}) |
| **Git** | Git | 코드 변경 이력을 추적하는 버전 관리 시스템. Claude Code와 함께 쓰면 실수를 쉽게 되돌릴 수 있음 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |

## ㄴ~ㄷ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **노드** | Node.js | Claude Code 실행에 필요한 JavaScript 런타임 환경. 버전 18 이상 필요 | [Day 0]({{ '/docs/day-0/installation.html' | relative_url }}) |
| **대화형 모드** | Interactive Mode | `claude` 명령어로 진입하는 기본 모드. 여러 번 주고받으며 작업하는 방식 | [Day 1]({{ '/docs/day-1/first-conversation.html' | relative_url }}) |
| **Diff** | Diff | 파일의 변경 전/후를 비교해서 보여주는 표시. 초록색은 추가, 빨간색은 삭제 | [Day 1]({{ '/docs/day-1/reading-and-writing.html' | relative_url }}) |

## ㄹ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **레포지토리** | Repository | Git으로 관리되는 프로젝트 저장소. 줄여서 “레포”라고도 함 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |
| **리팩토링** | Refactoring | 코드의 동작은 바꾸지 않으면서 구조를 개선하는 작업 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |
| **린트** | Lint | 코드의 스타일, 오류, 잠재적 문제를 자동으로 검사하는 도구. ESLint, Prettier 등 | [Day 4]({{ '/docs/day-4/hooks.html' | relative_url }}) |
| **LLM** | Large Language Model | 대규모 언어 모델. Claude, GPT 등 텍스트를 이해하고 생성하는 AI 모델의 총칭 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |

## ㅁ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **마크다운** | Markdown | 텍스트 서식을 간단한 기호(`#`, `**`, `-` 등)로 표현하는 문법. CLAUDE.md에서 사용 | [Day 2]({{ '/docs/day-2/claude-md.html' | relative_url }}) |
| **멀티에이전트** | Multi-Agent | 여러 AI 에이전트가 역할을 나눠 협업하는 시스템 구조 | [Day 7]({{ '/docs/day-7.html' | relative_url }}) |
| **멀티턴** | Multi-turn | 여러 차례 대화를 주고받는 방식. 복잡한 작업에서 자연스럽게 사용됨 | [Day 1]({{ '/docs/day-1/first-conversation.html' | relative_url }}) |
| **메모리** | Memory | CLAUDE.md를 통해 세션 간 유지되는 장기 기억 시스템 | [Day 2]({{ '/docs/day-2/claude-md.html' | relative_url }}) |
| **모델** | Model | Claude의 AI 두뇌. Haiku(빠름), Sonnet(균형), Opus(강력) 세 가지가 있음 | [치트시트]({{ '/docs/reference/cheatsheet.html' | relative_url }}) |
| **Max 플랜** | Max Plan | Anthropic 월정액 구독 플랜. API 키 없이 로그인만으로 Claude Code 사용 가능 | [FAQ]({{ '/docs/reference/faq.html' | relative_url }}) |
| **MCP** | Model Context Protocol | Claude가 외부 도구/서비스와 연결하는 표준 프로토콜. 웹 검색, DB 연결 등에 활용 | [Day 5]({{ '/docs/day-5/mcp.html' | relative_url }}) |

## ㅂ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **바이브 코딩** | Vibe Coding | 자연어로 원하는 것을 설명하면 AI가 코드를 생성하는 개발 방식 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |
| **번들러** | Bundler | 여러 파일을 하나로 묶어주는 도구. Webpack, Vite 등이 대표적 | - |
| **브랜치** | Branch | Git에서 독립적인 작업 흐름을 만드는 기능. 기능별로 분리해서 개발할 때 사용 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |

## ㅅ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **서브에이전트** | Sub-agent | Claude가 복잡한 작업을 처리하기 위해 내부적으로 실행하는 보조 에이전트 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **세션** | Session | Claude Code와의 하나의 대화 단위. `/clear`로 초기화, `--resume`으로 이어가기 가능 | [Day 1]({{ '/docs/day-1/first-conversation.html' | relative_url }}) |
| **스캐폴딩** | Scaffolding | 프로젝트의 기본 구조(폴더, 설정 파일 등)를 자동으로 생성하는 것 | [Day 1]({{ '/docs/day-1/reading-and-writing.html' | relative_url }}) |
| **슬래시 커맨드** | Slash Command | 세션 안에서 `/`로 시작하는 특수 명령어. `/compact`, `/cost`, `/model` 등 | [Day 1]({{ '/docs/day-1/basic-commands.html' | relative_url }}) |
| **스트리밍** | Streaming | AI 응답이 한꺼번에가 아니라 실시간으로 글자 단위로 출력되는 방식 | [Day 1]({{ '/docs/day-1/first-conversation.html' | relative_url }}) |

## ㅇ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **에이전트** | Agent | 도구를 사용하여 목표를 자율적으로 달성하는 AI 시스템. Claude Code가 대표적인 예 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |
| **에이전틱 코딩** | Agentic Coding | AI가 코드 생성을 넘어 파일 읽기/쓰기, 명령 실행까지 자율적으로 수행하는 개발 방식 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |
| **오케스트레이터** | Orchestrator | 여러 에이전트를 총괄하며 작업을 배분하고 결과를 종합하는 상위 에이전트 | [Day 7]({{ '/docs/day-7.html' | relative_url }}) |
| **원샷 모드** | One-shot Mode | `claude -p "질문"`으로 한 번만 질문하고 바로 종료하는 실행 방식 | [Day 1]({{ '/docs/day-1/basic-commands.html' | relative_url }}) |
| **API 키** | API Key | Anthropic 서비스 인증을 위한 고유 키. `sk-ant-api03-`으로 시작함 | [Day 0]({{ '/docs/day-0/api-key.html' | relative_url }}) |
| **Agent SDK** | Agent SDK | 코드에서 Claude Code를 프로그래밍 방식으로 제어할 수 있는 개발 도구 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |

## ㅈ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **자동 수락** | Auto-accept | 특정 도구 사용을 매번 확인하지 않고 자동으로 허용하는 설정 | [Day 4]({{ '/docs/day-4/hooks.html' | relative_url }}) |
| **자연어** | Natural Language | 프로그래밍 언어가 아닌 일상 언어(한국어, 영어 등). Claude Code는 자연어로 지시를 받음 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |
| **지시 파일** | Instruction File | CLAUDE.md 파일을 가리키는 말. 프로젝트별 규칙과 지시사항을 담고 있음 | [Day 2]({{ '/docs/day-2/claude-md.html' | relative_url }}) |

## ㅊ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **체이닝** | Chaining | 여러 명령이나 도구를 순서대로 연결해서 실행하는 것. 파이프라인과 유사한 개념 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **체크포인트** | Checkpoint | Claude Code가 작업 중 자동으로 만드는 복원 지점. 문제 발생 시 되돌리기 가능 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |

## ㅋ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **커밋** | Commit | Git에서 변경사항을 저장하는 단위. Claude Code가 메시지와 함께 자동 생성 가능 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |
| **컨텍스트** | Context | Claude가 현재 대화에서 참고하는 모든 정보. 이전 대화, 파일 내용, CLAUDE.md 등 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |
| **컨텍스트 윈도우** | Context Window | Claude가 한번에 기억할 수 있는 최대 범위 (200K 토큰). 초과 시 `/compact`로 압축 필요 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |
| **크레딧** | Credits | API 사용을 위한 선불 충전금. Anthropic Console에서 충전하고 사용량만큼 차감 | [FAQ]({{ '/docs/reference/faq.html' | relative_url }}) |
| **클라우드 MD** | CLAUDE.md | 프로젝트별/전역 지시사항을 담는 마크다운 파일. Claude Code의 “사용 설명서” 역할 | [Day 2]({{ '/docs/day-2/claude-md.html' | relative_url }}) |

## ㅌ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **터미널** | Terminal | 명령어를 텍스트로 입력하는 인터페이스. Claude Code가 실행되는 환경 | [Day 0]({{ '/docs/day-0/installation.html' | relative_url }}) |
| **토큰** | Token | AI가 텍스트를 처리하는 최소 단위. 한국어 한 글자는 보통 2~3 토큰에 해당 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |
| **툴 유즈** | Tool Use | Claude가 파일 읽기/쓰기, 명령 실행 등 도구를 사용하는 행위 | [Day 1]({{ '/docs/day-1/first-conversation.html' | relative_url }}) |

## ㅍ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **파이프** | Pipe | 한 명령의 출력을 다른 명령의 입력으로 전달하는 방식. `cat file | claude -p "분석해줘"` | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **프롬프트** | Prompt | Claude에게 하는 모든 말. 질문, 지시, 맥락 설명 등을 포함하는 개념 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |
| **프리커밋 훅** | Pre-commit Hook | Git 커밋 전에 자동 실행되는 검사 스크립트. 린트, 포맷팅, 테스트 등을 자동 실행 | [Day 4]({{ '/docs/day-4/hooks.html' | relative_url }}) |
| **풀 리퀘스트** | Pull Request (PR) | 코드 변경사항을 팀에 검토 요청하는 Git 기능. Claude Code가 생성과 리뷰 모두 가능 | [Day 2]({{ '/docs/day-2/git-workflow.html' | relative_url }}) |
| **Plan 모드** | Plan Mode | 실행 전에 계획을 먼저 세우도록 하는 프롬프트 전략. “먼저 계획 세워줘” 패턴 | [Day 3]({{ '/docs/day-3/prompting.html' | relative_url }}) |

## ㅎ

| 용어 | 영문 | 설명 | 관련 |
| --- | --- | --- | --- |
| **할루시네이션** | Hallucination | AI가 사실이 아닌 내용을 자신 있게 생성하는 현상. 코드 검증이 항상 필요한 이유 | [FAQ]({{ '/docs/reference/faq.html' | relative_url }}) |
| **헤드리스** | Headless Mode | 사람의 개입 없이 Claude Code를 자동으로 실행하는 모드. CI/CD 파이프라인 등에서 활용 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **훅** | Hook | 특정 시점에 자동으로 실행되는 코드. Claude Code Hooks(`PreToolUse`, `PostToolUse` 등)와 Git Hooks 모두 해당 | [Day 4]({{ '/docs/day-4/hooks.html' | relative_url }}) |

## 영문

| 용어 | 설명 | 관련 |
| --- | --- | --- |
| **CLI** | Command Line Interface. 텍스트 명령어로 소프트웨어를 제어하는 방식 | [Day 0]({{ '/docs/day-0/what-is-claude-code.html' | relative_url }}) |
| **Extended Thinking** | 복잡한 문제를 더 깊이 생각하는 Claude의 특수 모드. 긴 추론 과정을 거침 | [Day 7]({{ '/docs/day-7.html' | relative_url }}) |
| **IDE** | Integrated Development Environment. 통합 개발 환경 (VS Code, IntelliJ 등) | [Day 0]({{ '/docs/day-0/ide-setup.html' | relative_url }}) |
| **JSON** | 데이터를 표현하는 텍스트 형식. 설정 파일(`settings.json`, `mcp.json`)에 주로 사용 | - |
| **npm** | Node.js 패키지 관리자. Claude Code 설치 시 `npm install -g`로 사용 | [Day 0]({{ '/docs/day-0/installation.html' | relative_url }}) |
| **Skills** | Claude Code에 특정 전문성을 부여하는 지침 파일. 슬래시 커맨드로 호출 가능 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **Worktree** | Git의 격리된 작업 공간. 서브에이전트가 다른 브랜치에서 독립 작업할 때 활용 | [Day 6]({{ '/docs/day-6.html' | relative_url }}) |
| **YAML** | 설정 파일에 쓰이는 텍스트 형식. JSON보다 사람이 읽기 쉬움 | - |
| **YOLO 모드** | `--dangerously-skip-permissions` 플래그. 모든 권한을 자동 허용하는 위험한 실행 모드 | [치트시트]({{ '/docs/reference/cheatsheet.html' | relative_url }}) |

---

용어가 헷갈릴 때는 Claude Code 세션에서 “OOO가 뭐야?”라고 물어보면 현재 프로젝트 맥락에 맞게 설명해줍니다. 이 페이지를 북마크해두시면 편합니다.
