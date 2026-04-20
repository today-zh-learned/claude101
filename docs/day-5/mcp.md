---
title: "MCP — 외부 도구 연결하기"
---
# MCP — 외부 도구 연결하기

## 오늘의 목표

> MCP로 Claude Code에 눈, 귀, 손을 달아줍니다.

지금까지 Claude Code는 **터미널과 파일 시스템** 안에서만 일했습니다. 코드를 읽고 쓰고, 명령어를 실행하는 정도였죠. 오늘부터는 Claude가 **외부 세계와 연결**됩니다. GitHub 이슈를 읽고, Slack 메시지를 보내고, 데이터베이스를 조회하는 일이 가능해집니다.

그 열쇠가 바로 **MCP(Model Context Protocol)**입니다.

---

## MCP가 뭔가요?

컴퓨터에 USB 포트가 있습니다. 키보드를 꽂으면 타이핑할 수 있고, 마우스를 꽂으면 클릭할 수 있고, 외장 하드를 꽂으면 저장 공간이 늘어납니다. **USB 포트 자체는 하나의 규격**이지만, 뭘 꽂느냐에 따라 능력이 달라집니다.

MCP는 Claude의 USB 포트입니다. MCP라는 **표준 프로토콜**을 통해 외부 도구(MCP 서버)를 연결하면, Claude가 그 도구의 기능을 사용할 수 있게 됩니다.

| 연결하는 것 | Claude가 얻는 능력 |
| --- | --- |
| GitHub MCP 서버 | 이슈 조회, PR 생성, 코드 리뷰 |
| Slack MCP 서버 | 채널 메시지 읽기/보내기 |
| PostgreSQL MCP 서버 | DB 쿼리 실행, 스키마 조회 |
| Puppeteer MCP 서버 | 웹 브라우저 조작, 스크린샷 |
| Filesystem MCP 서버 | 지정 폴더 외부 파일 접근 |

---

## MCP 없는 Claude vs MCP 있는 Claude

차이를 체감해봅시다.

**MCP 없이**: “GitHub 이슈 목록 좀 봐줘”라고 하면, Claude는 `gh issue list` CLI 명령어를 실행합니다. 동작은 하지만, GitHub CLI가 설치되어 있어야 하고 인증도 따로 해둬야 합니다.

**MCP 있으면**: Claude가 GitHub MCP 서버를 통해 **API로 직접** 이슈 목록을 가져옵니다. 더 풍부한 데이터를 더 안정적으로 받을 수 있습니다. 그리고 이 방식은 Claude가 **도구의 존재를 인식**하고 있어서, 필요한 순간에 알아서 활용합니다.

> ℹ️ **정보**
>
> MCP는 Claude Code뿐 아니라 Claude Desktop App에서도 사용할 수 있습니다. 프로토콜이 동일하므로 한 번 이해하면 양쪽 모두 활용 가능합니다.

---

## 설정 파일 위치

MCP 서버를 설정하는 방법은 두 가지입니다.

**방법 1**: `/mcp` 명령어를 사용합니다 (가장 간편).

`> /mcp add github npx -y @modelcontextprotocol/server-github`
**방법 2**: `.claude/settings.json` 파일에 직접 작성합니다. 프로젝트 루트에 이 파일이 있으면 Claude Code가 시작할 때 자동으로 읽습니다.

`// .claude/settings.json
{
  "mcpServers": {
    "서버이름": {
      "command": "실행 명령어",
      "args": ["인자1", "인자2"],
      "env": {
        "환경변수": "값"
      }
    }
  }
}`
각 MCP 서버는 `command`로 실행되는 별도 프로세스입니다. Claude Code가 시작되면 이 프로세스들을 함께 띄우고, 표준 입출력으로 통신합니다.

---

## 실습 1: GitHub MCP 서버 연결하기

가장 많이 쓰이는 GitHub MCP 서버를 연결해봅시다.

**Step 1**: `.claude/settings.json` 파일을 만듭니다 (이미 있으면 수정합니다).

`{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_여러분의토큰"
      }
    }
  }
}`
**Step 2**: GitHub Personal Access Token이 필요합니다. GitHub > Settings > Developer settings > Personal access tokens에서 생성합니다. `repo` 스코프를 선택하면 됩니다.

**Step 3**: Claude Code를 재시작합니다.

`claude`
**Step 4**: 연결을 확인합니다.

`> 이 프로젝트의 GitHub 이슈 목록 보여줘`
Claude가 GitHub MCP 서버를 통해 이슈를 조회하면 성공입니다.

> ⚠️ **주의**
>
> 토큰을 settings.json에 직접 넣으면 Git에 커밋될 위험이 있습니다. `.gitignore`에 `.claude/settings.json`을 추가하거나, 환경변수로 관리하는 것을 권장합니다.

---

## 실습 2: Filesystem MCP 서버 연결하기

Claude Code는 기본적으로 현재 프로젝트 폴더에만 접근합니다. Filesystem MCP 서버를 연결하면 **다른 폴더의 파일**도 읽고 쓸 수 있습니다.

`{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/me/documents",
        "/Users/me/downloads"
      ]
    }
  }
}`
`args`의 마지막 항목들이 접근을 허용할 디렉토리 경로입니다. 필요한 폴더만 명시적으로 열어주는 방식이라 보안 측면에서도 합리적입니다.

---

## 인기 MCP 서버 5개

현재 가장 널리 쓰이는 MCP 서버들입니다.

| MCP 서버 | 패키지명 | 주요 기능 |
| --- | --- | --- |
| **GitHub** | `@modelcontextprotocol/server-github` | 이슈, PR, 코드 검색, 리뷰 |
| **Slack** | `@modelcontextprotocol/server-slack` | 채널 메시지 읽기/쓰기, 검색 |
| **PostgreSQL** | `@modelcontextprotocol/server-postgres` | SQL 쿼리, 스키마 조회 |
| **Puppeteer** | `@modelcontextprotocol/server-puppeteer` | 웹 페이지 조작, 스크린샷 |
| **Filesystem** | `@modelcontextprotocol/server-filesystem` | 프로젝트 외부 파일 접근 |

공식 목록 외에도 커뮤니티에서 만든 MCP 서버가 계속 늘어나고 있습니다. [MCP 공식 사이트](https://modelcontextprotocol.io)에서 전체 목록을 확인할 수 있습니다.

---

## 여러 MCP 서버 동시에 쓰기

MCP의 진짜 힘은 **여러 도구를 동시에 연결**할 때 나옵니다.

`{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": { "SLACK_BOT_TOKEN": "xoxb-..." }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": { "DATABASE_URL": "postgresql://..." }
    }
  }
}`
이렇게 연결하면 이런 요청이 가능합니다.

`> GitHub에서 오늘 올라온 이슈를 확인하고,
  관련된 DB 테이블 스키마를 조회한 다음,
  분석 결과를 Slack #dev 채널에 요약해서 보내줘`
하나의 대화에서 세 가지 외부 도구를 넘나들며 작업합니다. 이것이 MCP가 단순한 API 래퍼와 다른 점입니다. Claude가 **맥락을 유지하면서** 여러 도구를 조합합니다.

> ℹ️ **정보**
>
> AI싱크클럽에서는 GitHub + Slack + DB를 동시에 연결해서 씁니다. MCP가 진짜 힘을 발휘하는 건 이렇게 여러 도구가 연결될 때입니다. 도구 하나하나는 단순해도, 조합하면 자동화의 범위가 크게 넓어집니다.

---

## 정리

- MCP는 Claude에 외부 도구를 연결하는 **표준 프로토콜**입니다 (USB 포트 비유)

- `.claude/settings.json`에 MCP 서버를 등록하면 Claude가 자동으로 인식합니다

- GitHub, Slack, PostgreSQL, Puppeteer, Filesystem 등 다양한 서버가 있습니다

- **여러 MCP 서버를 동시에 연결**하면 도구 간 조합으로 자동화 범위가 넓어집니다

- 토큰/키는 `.gitignore`로 보호하거나 환경변수로 관리합니다

다음은 상황에 맞게 AI 모델을 골라 쓰는 법을 배웁니다.

-> [모델 골라 쓰기](/docs/day-5/multi-model)
