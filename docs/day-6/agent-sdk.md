---
title: "Agent SDK — 코드로 제어"
---
# Agent SDK — 코드로 제어

## 오늘의 목표

> JavaScript/Python 코드에서 Claude Code를 프로그래밍 방식으로 사용하기

Headless 모드가 “터미널 명령어로 Claude를 부르는 것”이었다면, Agent SDK는 **코드 안에서 Claude를 부르는 것**입니다.

---

## Agent SDK란?

Agent SDK는 **내 프로그램에서 Claude Code를 부르는 전화번호**입니다. 웹사이트, 슬랙봇, 자동화 도구 등 어떤 프로그램이든 SDK를 통해 Claude Code의 모든 기능을 사용할 수 있습니다.

`일반 사용:     사람 → 터미널 → Claude Code
Headless:     스크립트 → 터미널 → Claude Code
Agent SDK:    내 프로그램 → SDK → Claude Code   ← 가장 유연함`
---

## 설치

TypeScript/JavaScript 프로젝트에서 사용합니다.

`npm install @anthropic-ai/claude-code`
> ℹ️ **정보**
>
> Python SDK도 있습니다. `pip install claude-code-sdk`로 설치할 수 있습니다. 이 문서에서는 TypeScript를 기준으로 설명하지만, Python도 거의 같은 구조입니다.

---

## 기본 사용법

가장 간단한 예제부터 봅시다.

`import { claude } from "@anthropic-ai/claude-code";
 
async function main() {
  // Claude에게 작업 지시
  const result = await claude({
    prompt: "src 폴더 구조를 분석해줘",
    options: {
      maxTurns: 3,       // 최대 3번의 도구 사용
    }
  });
 
  console.log(result.stdout);
}
 
main();`
`claude()` 함수 하나로 Claude Code의 모든 기능을 쓸 수 있습니다. 파일 읽기, 코드 분석, 검색 등 터미널에서 하던 모든 것이 가능합니다.

### 핵심 옵션

| 옵션 | 설명 | 기본값 |
| --- | --- | --- |
| `prompt` | Claude에게 보낼 지시 | (필수) |
| `maxTurns` | 도구 사용 최대 횟수 | 무제한 |
| `allowedTools` | 허용할 도구 목록 | 전체 |
| `cwd` | 작업 디렉토리 | 현재 디렉토리 |

---

## 세션 관리

대화를 이어가려면 세션을 유지해야 합니다. 비유하자면, 전화를 끊지 않고 계속 대화하는 것입니다.

`import { claude } from "@anthropic-ai/claude-code";
 
async function analyzeAndFix() {
  // 1단계: 분석
  const analysis = await claude({
    prompt: "src/utils.ts에서 버그를 찾아줘",
    options: { maxTurns: 5 }
  });
 
  console.log("분석 결과:", analysis.stdout);
 
  // 2단계: 같은 세션에서 수정 요청
  const fix = await claude({
    prompt: "찾은 버그를 수정해줘",
    options: {
      maxTurns: 5,
      sessionId: analysis.sessionId,  // 이전 대화 이어가기
    }
  });
 
  console.log("수정 결과:", fix.stdout);
}`
`sessionId`를 전달하면 Claude가 이전 대화 내용을 기억합니다. 분석 → 수정처럼 연속된 작업에 유용합니다.

---

## 도구 접근 제어

보안을 위해 Claude가 사용할 수 있는 도구를 제한할 수 있습니다.

`// 읽기만 허용 (안전 모드)
const readOnly = await claude({
  prompt: "코드를 분석해줘",
  options: { allowedTools: ["Read", "Grep", "Glob"] }
});
 
// Bash까지 허용 (테스트 실행 등)
const withBash = await claude({
  prompt: "테스트를 실행하고 결과를 알려줘",
  options: { allowedTools: ["Read", "Grep", "Glob", "Bash"] }
});`
> ⚠️ **주의**
>
> `Write`와 `Edit` 도구를 허용하면 Claude가 파일을 수정할 수 있습니다. 프로덕션 코드에 대해서는 읽기 전용으로 시작하고, 결과를 검증한 뒤에 쓰기 권한을 부여하세요.

---

## 실습: 코드 분석 스크립트 만들기

실제로 동작하는 코드 분석기를 만들어봅시다.

**Step 1**: 프로젝트를 초기화합니다.

`mkdir code-analyzer && cd code-analyzer
npm init -y
npm install @anthropic-ai/claude-code typescript tsx`
**Step 2**: `analyze.ts` 파일을 만듭니다.

`import { claude } from "@anthropic-ai/claude-code";
 
async function analyzeProject(targetDir: string) {
  // 1. 프로젝트 구조 파악
  const structure = await claude({
    prompt: "폴더 구조를 파악하고 기술 스택을 3줄로 요약해줘",
    options: {
      maxTurns: 3,
      allowedTools: ["Read", "Grep", "Glob"],
      cwd: targetDir,
    }
  });
  console.log("[요약]", structure.stdout);
 
  // 2. 같은 세션에서 개선점 탐색
  const issues = await claude({
    prompt: "개선할 점을 3가지만 찾아줘. 파일명과 줄 번호 포함",
    options: {
      maxTurns: 5,
      allowedTools: ["Read", "Grep", "Glob"],
      cwd: targetDir,
      sessionId: structure.sessionId,
    }
  });
  console.log("[개선 제안]", issues.stdout);
}
 
const targetDir = process.argv[2] || ".";
analyzeProject(targetDir);`
**Step 3**: 실행합니다.

`npx tsx analyze.ts /path/to/my-project`
> ℹ️ **정보**
>
> 이 스크립트는 읽기 전용입니다. 코드를 수정하지 않으므로 어떤 프로젝트에 실행해도 안전합니다.

---

## 정리

- Agent SDK는 **코드 안에서 Claude Code를 호출하는 라이브러리**입니다

- `npm install @anthropic-ai/claude-code`로 설치합니다

- `claude()` 함수 하나로 모든 기능을 사용할 수 있습니다

- `sessionId`로 대화를 이어갈 수 있습니다

- `allowedTools`로 보안을 제어합니다

- 웹앱, 슬랙봇, 자동화 도구 등 어디서든 Claude를 통합할 수 있습니다

다음은 하나의 Claude가 다른 Claude를 부리는 서브에이전트 패턴을 배웁니다.

> [다음: 서브에이전트 패턴 →]({{ '/docs/day-6/subagents.html' | relative_url }})
