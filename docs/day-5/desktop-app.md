---
title: "Desktop App 활용"
---
# Desktop App 활용

## 오늘의 목표

> Claude Desktop App과 CLI(Claude Code)의 차이를 이해하고, 둘을 함께 활용합니다.

“Claude”라고 하면 떠오르는 게 여러 가지입니다. 웹에서 쓰는 claude.ai, 터미널에서 쓰는 Claude Code, 그리고 **Claude Desktop App**이 있습니다. 이름은 비슷하지만 용도가 다릅니다. 오늘은 Desktop App의 위치를 정리하고, Claude Code와 어떻게 조합하면 좋은지 알아봅니다.

---

## Claude Desktop App과 Claude Code는 다릅니다

혼동하기 쉬운 부분이라 먼저 정리합니다.

| 구분 | Claude Desktop App | Claude Code (CLI) |
| --- | --- | --- |
| **실행 방식** | 데스크톱 앱 (GUI) | 터미널 명령어 |
| **주요 용도** | 일반 대화, 문서 작업, 분석 | 코딩, 파일 조작, 자동화 |
| **파일 접근** | MCP를 통해서만 가능 | 프로젝트 폴더에 직접 접근 |
| **코드 실행** | 불가 | bash 명령어 직접 실행 |
| **MCP 지원** | 지원 | 지원 |
| **대화 저장** | 자동 저장 | 세션 종료 시 사라짐 |

한마디로, **Desktop App은 대화형 어시스턴트**이고, **Claude Code는 코딩 파트너**입니다.

Desktop App은 Anthropic 공식 사이트에서 다운로드할 수 있습니다. macOS와 Windows를 지원합니다.

---

## Desktop App이 잘하는 것

Desktop App은 이런 작업에 강합니다.

**문서 분석과 요약**: PDF, 이미지, 긴 텍스트를 드래그 앤 드롭으로 넣고 분석을 요청할 수 있습니다. 터미널에서 하기 번거로운 작업입니다.

**아이디어 브레인스토밍**: 코드와 무관한 기획, 전략, 글쓰기 작업은 GUI 환경이 더 편합니다. 대화 히스토리도 자동 저장됩니다.

**MCP를 통한 외부 연결**: Desktop App도 MCP를 지원합니다. Claude Code와 동일한 MCP 서버들을 연결할 수 있습니다.

**비개발자와 협업**: 팀에 터미널을 안 쓰는 분이 있다면, Desktop App으로 같은 MCP 서버에 접근하게 할 수 있습니다.

---

## Claude Code가 잘하는 것

반면 이런 작업은 Claude Code(CLI)가 압도적으로 좋습니다.

**코드 직접 수정**: 파일을 읽고, 수정하고, 저장하는 것이 자연스럽습니다.

**명령어 실행**: `npm install`, `git commit`, `docker build` 같은 명령어를 Claude가 직접 실행합니다.

**프로젝트 맥락 유지**: CLAUDE.md를 자동으로 읽고, 프로젝트 구조를 파악한 상태에서 일합니다.

**자동화 파이프라인**: 스크립트에서 Claude를 호출하여 반복 작업을 자동화할 수 있습니다.

---

## 둘을 함께 쓰면 좋은 이유

Desktop App과 Claude Code를 **같이** 쓰면 작업 효율이 올라갑니다.

### 패턴 1: 기획은 Desktop, 구현은 CLI

`Desktop App: "사용자 인증 시스템을 설계해줘.
             JWT vs 세션 방식의 장단점을 비교하고,
             우리 프로젝트에 맞는 방식을 추천해줘"

(설계 완료 후)

Claude Code: "방금 정리한 JWT 기반 인증을 구현해줘.
             미들웨어 + 라우트 + 토큰 갱신 로직 포함"`
### 패턴 2: 리서치는 Desktop, 적용은 CLI

`Desktop App: "Next.js App Router에서 병렬 라우트가 뭔지 설명해줘.
             코드 예시도 보여줘"

(이해 후)

Claude Code: "우리 프로젝트의 대시보드 페이지에
             병렬 라우트를 적용해줘"`
### 패턴 3: 에러 분석은 Desktop, 수정은 CLI

터미널에서 긴 에러 로그가 나왔을 때, Desktop App에 복사해서 “이 에러 분석해줘”라고 하면 편합니다. 분석이 끝나면 Claude Code에서 수정을 진행합니다.

---

## Desktop App MCP 설정법

Desktop App의 MCP 설정 파일은 Claude Code와 **위치가 다릅니다**.

| 환경 | 설정 파일 위치 |
| --- | --- |
| Claude Code | `.claude/settings.json` (프로젝트별) |
| Desktop App | `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) |

Desktop App에서 MCP를 설정하려면 `claude_desktop_config.json`을 편집합니다.

`{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_여러분의토큰"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/me/projects"
      ]
    }
  }
}`
형식은 Claude Code의 settings.json과 동일합니다. 같은 MCP 서버 설정을 양쪽에 넣어두면, **어디서든 같은 외부 도구에 접근**할 수 있습니다.

Desktop App의 설정 파일을 수정한 후에는 앱을 완전히 종료했다가 다시 열어야 합니다. 단순 새로고침으로는 반영되지 않습니다.

---

## 어떤 걸 먼저 익혀야 하나요?

이 플레이북을 따라오고 있다면 **Claude Code가 우선**입니다. 코딩 자동화와 프로젝트 관리가 핵심 가치이기 때문입니다.

Desktop App은 보조 도구로 두고, 필요할 때 꺼내 쓰면 됩니다. 두 도구 모두 MCP를 지원하므로, MCP 서버를 익혀두면 양쪽에서 활용할 수 있습니다.

---

## 정리

- Claude Desktop App은 **GUI 기반 대화형 어시스턴트**, Claude Code는 **터미널 기반 코딩 파트너**

- 기획/리서치는 Desktop App, 구현/자동화는 Claude Code로 나누면 효율적입니다

- Desktop App도 MCP를 지원하며, 설정 파일 위치만 다릅니다 (`claude_desktop_config.json`)

- 두 도구에 같은 MCP 서버를 연결하면 어디서든 동일한 외부 도구를 사용할 수 있습니다

다음은 웹과 원격 환경에서 Claude Code를 사용하는 방법을 알아봅니다.

-> [웹에서도 쓸 수 있어요](/docs/day-5/web-and-remote)
