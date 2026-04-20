---
title: "Hooks — 자동 반응 만들기"
---
# Hooks — 자동 반응 만들기

## 오늘의 목표

> Hooks로 Claude Code가 특정 상황에 자동으로 반응하게 만들기

Day 3까지는 Claude에게 직접 말을 걸었습니다. 오늘부터는 **말하지 않아도 알아서 움직이게** 만듭니다. 그 첫 번째 도구가 Hooks입니다.

---

## Hooks란?

IFTTT를 아시나요? “이런 일이 생기면 → 이걸 해라”를 설정하는 서비스입니다.

Hooks도 같은 개념입니다. Claude Code가 특정 작업을 할 때, **자동으로 추가 동작을 실행**하게 만드는 장치입니다.

`일반 작업:   Claude가 파일 수정 → 끝
Hooks 적용:  Claude가 파일 수정 → 자동으로 린트 실행 → 끝`
코드 품질을 지키고 싶은데 매번 “린트 돌려줘”라고 말하기 귀찮다면, Hooks로 자동화하면 됩니다.

---

## 4가지 훅 타입

Hooks는 **언제 실행되느냐**에 따라 4가지로 나뉩니다.

| 훅 타입 | 언제 실행되나 | 비유 |
| --- | --- | --- |
| **PreToolUse** | Claude가 도구를 쓰기 **전에** | ”문 열기 전에 노크부터 해” |
| **PostToolUse** | Claude가 도구를 쓴 **후에** | ”문 닫고 나서 불 꺼” |
| **Notification** | Claude가 알림을 보낼 **때** | ”알림 오면 소리 울려” |
| **SessionStart** | 대화가 시작될 **때** | ”출근하면 커피 먼저 내려” |

처음에는 `PreToolUse`와 `PostToolUse`만 알면 충분합니다. 이 두 가지가 가장 자주 쓰입니다.

> ℹ️ **정보**
>
> “도구(Tool)“는 Claude Code가 작업에 사용하는 기능을 말합니다. 파일 읽기(`Read`), 파일 쓰기(`Write`), 명령어 실행(`Bash`) 등이 모두 도구입니다.

---

## 설정 파일 위치와 형식

Hooks는 Claude Code 설정 파일에 JSON으로 작성합니다.

`# 설정 파일 열기
claude config edit`
설정 파일 위치는 두 곳입니다:

| 범위 | 경로 | 용도 |
| --- | --- | --- |
| **프로젝트** | `.claude/settings.json` | 이 프로젝트에서만 적용 |
| **사용자** | `~/.claude/settings.json` | 모든 프로젝트에 적용 |

Hooks 설정의 기본 구조는 이렇습니다:

`{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "도구 이름 패턴",
        "hook": "실행할 명령어"
      }
    ]
  }
}`
---

## 실습 1: 커밋할 때 자동으로 린트 실행하기

Git 커밋을 할 때마다 자동으로 코드 검사를 돌려봅시다.

프로젝트의 `.claude/settings.json` 파일에 다음을 추가합니다:

`{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hook": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -q 'git commit'; then npm run lint 2>/dev/null; fi"
      }
    ]
  }
}`
이 훅이 하는 일을 풀어서 설명하면:

`Claude가 Bash 도구를 쓴 후에 (PostToolUse + Bash)
  → 그 명령어가 git commit을 포함하면
    → npm run lint를 자동으로 실행`
> ⚠️ **주의**
>
> 린트 도구(`eslint`, `prettier` 등)가 프로젝트에 설치되어 있어야 합니다. 설치가 안 되어 있으면 이 훅은 조용히 무시됩니다.

이제 Claude에게 “커밋해줘”라고 말하면, 커밋 후 자동으로 린트가 돌아갑니다. 매번 따로 부탁하지 않아도 됩니다.

---

## 실습 2: 특정 파일 수정 차단하기

실수로 건드리면 안 되는 파일이 있습니다. 예를 들어 환경 설정 파일(`.env`)이나 프로덕션 설정 파일 같은 것들이죠.

이런 파일을 Claude가 수정하지 못하게 막아봅시다:

`{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hook": "if echo \"$CLAUDE_TOOL_INPUT\" | grep -qE '\\.env|\\.production'; then echo 'BLOCK: 이 파일은 수정이 차단되었습니다' >&2; exit 1; fi"
      }
    ]
  }
}`
`PreToolUse`라서 **도구가 실행되기 전에** 차단합니다. 파일이 이미 수정된 후가 아니라, 수정 시도 자체를 막는 거죠.

> ℹ️ **정보**
>
> `matcher`에 `Write|Edit`처럼 파이프(`|`)를 쓰면 “Write **또는** Edit” 도구에 반응합니다. 정규식 패턴을 지원합니다.

---

## 여러 훅 조합하기

한 가지 타입에 여러 훅을 넣을 수 있습니다. 배열로 나열하면 됩니다:

`{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hook": "파일 보호 스크립트"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hook": "린트 스크립트"
      }
    ],
    "SessionStart": [
      {
        "hook": "echo '작업 시작: '$(date) >> ~/claude-log.txt"
      }
    ]
  }
}`
`SessionStart`는 `matcher`가 필요 없습니다. 대화가 시작될 때 무조건 실행되니까요.

---

## AI싱크클럽 실전: 지침 파일 보호

AI싱크클럽에서는 23개 에이전트의 지침 파일을 관리합니다. 이 파일들이 실수로 수정되면 전체 시스템이 흔들릴 수 있죠.

그래서 Hooks로 **지침 파일 수정을 감시**합니다:

`{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hook": "python3 .claude/hooks/guideline-guard.py"
      }
    ]
  }
}`
Claude가 파일을 수정하려 할 때마다 이 스크립트가 실행되고, 대상이 지침 파일이면 사용자 승인을 요구합니다. **자동화와 안전장치를 동시에** 구현하는 것이죠.

> ⚠️ **주의**
>
> 지침 파일을 보호하는 건 실전에서 매우 중요합니다. AI가 실수로 자기 자신의 규칙을 바꿔버리면 예상치 못한 동작이 발생할 수 있습니다.

---

## 정리

오늘 배운 것을 정리합니다:

- Hooks는 “이런 일이 생기면 → 이걸 해라”를 자동화하는 장치입니다

- 4가지 타입이 있고, `PreToolUse`와 `PostToolUse`가 가장 많이 쓰입니다

- `.claude/settings.json`에 JSON 형식으로 설정합니다

- 린트 자동 실행, 파일 보호 등 실용적인 자동화를 만들 수 있습니다

- AI싱크클럽에서는 지침 파일 보호에 Hooks를 활용하고 있습니다

다음은 자주 하는 작업을 슬래시 커맨드로 만들어봅시다.

> [다음: 나만의 명령어 만들기 →](/docs/day-4/custom-commands)
