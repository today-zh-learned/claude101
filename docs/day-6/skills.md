---
title: "Skills — AI에게 전문성 부여"
---
# Skills — AI에게 전문성 부여

## 오늘의 목표

> Skills 시스템으로 Claude에게 특정 분야 전문가 역할을 맡기기

Day 5까지 Claude Code의 기본기를 탄탄히 다졌습니다. Day 6부터는 **에이전트 시스템**의 세계로 들어갑니다. 첫 번째 주제는 Skills입니다.

---

## Skills란?

회사에 신입 직원이 들어왔다고 상상해보세요. CLAUDE.md가 “우리 회사 전체 규칙”을 알려주는 사원 핸드북이라면, Skills는 **특정 업무의 전문 매뉴얼**입니다.

예를 들어 같은 직원에게 “오늘은 코드 리뷰해줘”라고 하면 코드 리뷰 매뉴얼을, “오늘은 테스트 작성해줘”라고 하면 테스트 작성 매뉴얼을 건네는 겁니다. 상황에 맞는 **전문 지침**을 꺼내 쓰는 것이죠.

---

## Skills vs CLAUDE.md

이 두 가지를 혼동하기 쉽습니다. 명확히 구분해봅시다.

| 구분 | CLAUDE.md | Skills |
| --- | --- | --- |
| **역할** | 프로젝트 전체 규칙 | 특정 작업 전문 지침 |
| **적용 시점** | 항상 자동 로드 | 필요할 때만 호출 |
| **비유** | 사원 핸드북 | 업무별 전문 매뉴얼 |
| **예시** | ”TypeScript를 사용한다" | "코드 리뷰 시 보안 취약점을 반드시 체크한다” |

CLAUDE.md에 모든 것을 넣으면 어떻게 될까요? 파일이 너무 길어지고, 코드 리뷰할 때 테스트 작성 규칙까지 읽게 됩니다. Skills를 쓰면 **필요한 전문 지식만 정확히** 전달할 수 있습니다.

> ℹ️ **정보**
>
> CLAUDE.md는 “항상 알아야 할 것”, Skills는 “지금 이 작업에 필요한 것”으로 나누면 됩니다.

---

## Skill 파일 구조

Skill은 프로젝트의 `.claude/commands/` 폴더에 마크다운 파일로 만듭니다.

`my-project/
├── .claude/
│   └── commands/
│       ├── code-review.md      ← 코드 리뷰 Skill
│       ├── write-test.md       ← 테스트 작성 Skill
│       └── refactor.md         ← 리팩토링 Skill
├── src/
├── CLAUDE.md
└── package.json`
각 `.md` 파일이 하나의 Skill입니다. 파일 이름이 곧 명령어 이름이 됩니다.

### 기본 형태

`# 코드 리뷰 가이드
 
이 프로젝트의 코드를 리뷰할 때 다음 기준을 따르세요:
 
## 체크 항목
1. 타입 안전성: any 사용을 지양합니다
2. 에러 처리: try-catch가 적절히 사용되었는지 확인합니다
3. 보안: 사용자 입력값이 검증되었는지 확인합니다
4. 성능: 불필요한 렌더링이나 루프가 없는지 확인합니다
 
## 리뷰 결과 형식
- ✅ 통과 항목
- ⚠️ 개선 권장 항목
- ❌ 반드시 수정 항목`
### YAML 프론트매터 활용

파일 상단에 메타데이터를 추가할 수 있습니다.

`---
description: 코드 리뷰를 수행합니다
allowed-tools: Read, Grep, Glob
---
 
# 코드 리뷰 가이드
...`
| 필드 | 설명 |
| --- | --- |
| `description` | Skill의 설명. 슬래시 메뉴에 표시됩니다 |
| `allowed-tools` | 이 Skill에서 사용할 수 있는 도구를 제한합니다 |

---

## 실습: 코드 리뷰 Skill 만들기

직접 만들어봅시다.

**Step 1**: `.claude/commands/` 폴더를 만듭니다.

`mkdir -p .claude/commands`
**Step 2**: `code-review.md` 파일을 생성합니다.

`---
description: PR 수준의 코드 리뷰를 수행합니다
---
 
# 코드 리뷰
 
변경된 파일을 분석하고 다음 기준으로 리뷰해주세요:
 
## 필수 체크
1. **타입 안전성** — any 타입 사용 여부
2. **에러 처리** — 예외 상황 대응 여부
3. **보안** — 입력값 검증, SQL 인젝션 등
4. **네이밍** — 변수/함수명이 의도를 명확히 전달하는지
 
## 출력 형식
각 파일별로 다음 형식으로 정리해주세요:
 
### [파일명]
- ✅ 잘된 점
- ⚠️ 개선하면 좋을 점
- ❌ 반드시 수정할 점`
**Step 3**: Claude Code에서 호출합니다.

`> /project:code-review`
`/project:` 뒤에 파일 이름(확장자 제외)을 붙이면 해당 Skill이 실행됩니다.

> ⚠️ **주의**
>
> Skill 파일을 수정한 뒤에는 Claude Code를 **재시작**해야 변경 사항이 반영됩니다. `/quit` 후 다시 `claude`를 실행하세요.

---

## 프로젝트 Skills vs 개인 Skills

Skills는 두 군데에 놓을 수 있습니다.

| 위치 | 경로 | 공유 범위 |
| --- | --- | --- |
| **프로젝트 Skills** | `.claude/commands/` | 이 프로젝트를 쓰는 모든 사람 |
| **개인 Skills** | `~/.claude/commands/` | 내 컴퓨터의 모든 프로젝트 |

팀 전체가 공유할 리뷰 기준은 프로젝트 Skills에, 나만 쓰는 개인 작업 패턴은 개인 Skills에 넣으면 됩니다.

`# 개인 Skills 폴더 만들기
mkdir -p ~/.claude/commands`
---

## 실전 활용 패턴

Skills를 잘 쓰는 팀은 이런 패턴을 만듭니다.

`.claude/commands/
├── code-review.md        # 코드 리뷰
├── write-test.md         # 테스트 코드 작성
├── create-component.md   # 새 컴포넌트 생성 (폴더 구조 포함)
├── debug.md              # 버그 디버깅 절차
└── deploy-checklist.md   # 배포 전 체크리스트`
> ℹ️ **정보**
>
> AI싱크클럽의 youtube_maker 프로젝트에서는 `.claude/agents/` 폴더에 21개의 전문 에이전트 정의를 두고 있습니다. 이것도 Skills의 확장된 형태입니다. 에이전트마다 전문 분야가 다르고, 오케스트레이터가 상황에 맞는 에이전트를 호출합니다.

---

## 정리

- Skills는 **특정 작업의 전문 매뉴얼**입니다

- CLAUDE.md가 전체 규칙이라면, Skills는 상황별 전문 지침입니다

- `.claude/commands/` 폴더에 마크다운 파일로 만듭니다

- `/project:파일명`으로 호출합니다

- 프로젝트 Skills(팀 공유)와 개인 Skills(나만 사용)를 구분할 수 있습니다

다음은 Claude Code를 사람 없이 자동으로 실행하는 방법을 배웁니다.

> [다음: Headless — 사람 없이 실행 →](/docs/day-6/headless)
