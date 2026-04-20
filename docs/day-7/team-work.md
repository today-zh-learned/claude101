---
title: "팀으로 함께 쓰기"
---
# 팀으로 함께 쓰기

## 오늘의 목표

> 팀 환경에서 Claude Code를 효과적으로 도입하고, 일관된 워크플로우를 만들기

혼자 쓸 때는 자유롭게 써도 됩니다. 하지만 팀으로 쓸 때는 규칙이 필요합니다. 같은 도구를 쓰더라도 사람마다 다르게 쓰면 혼란만 커집니다.

이 페이지에서는 팀에서 Claude Code를 도입하고 운영하는 방법을 다룹니다.

---

## CLAUDE.md를 팀 규칙서로 활용

Day 2에서 CLAUDE.md를 프로젝트 설명서로 만들었습니다. 팀 환경에서는 이것이 **팀 규칙서**가 됩니다.

`# CLAUDE.md (팀용 예시)
 
## 프로젝트 규칙
- 코드 스타일: ESLint + Prettier 설정을 따른다
- 커밋 메시지: Conventional Commits 형식을 따른다
- 테스트: 새 기능은 반드시 테스트 코드를 포함한다
- 문서: 공개 API는 JSDoc 주석을 작성한다
 
## 금지 사항
- node_modules/ 직접 수정 금지
- main 브랜치에 직접 push 금지
- console.log를 프로덕션 코드에 남기지 않을 것
 
## 코드 리뷰 기준
- 보안: 입력 검증이 되어 있는가?
- 성능: 불필요한 렌더링이 없는가?
- 가독성: 다른 팀원이 이해할 수 있는가?`
이 파일을 Git에 커밋하면, **팀원 전체가 같은 규칙으로 Claude Code를 사용**하게 됩니다. 새 팀원이 합류해도 CLAUDE.md만 읽으면 프로젝트 규칙을 파악할 수 있습니다.

---

## 팀원별 권한 모드 설정

Claude Code는 실행 권한 수준을 설정할 수 있습니다.

| 모드 | 설명 | 추천 대상 |
| --- | --- | --- |
| **기본 모드** | 파일 읽기/쓰기 시 확인 요청 | 신규 팀원 |
| **자동 허용** | 지정한 도구를 확인 없이 실행 | 숙련된 팀원 |

`# 기본 모드: 모든 동작에 확인 요청 (신규 팀원 추천)
claude
 
# 자동 허용: 읽기, 쓰기, Bash 자동 허용 (숙련된 팀원)
claude --allowedTools "Read,Write,Bash"`
### .claude/settings.json으로 팀 설정 통일

`{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)"
    ]
  }
}`
이 설정 파일을 Git에 커밋하면, 팀원 모두 같은 권한 정책을 사용합니다. 위험한 명령어는 팀 차원에서 차단할 수 있습니다.

`git push --force`나 `rm -rf` 같은 파괴적 명령어는 팀 설정에서 차단하는 것을 강력히 권장합니다. 실수 한 번이 큰 피해로 이어질 수 있습니다.

---

## 코드 리뷰 자동화

Claude Code로 PR 리뷰를 자동화할 수 있습니다.

### GitHub Actions로 자동 리뷰

`# .github/workflows/claude-review.yml
name: Claude Code Review
 
on:
  pull_request:
    types: [opened, synchronize]
 
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
 
      - name: Claude Code 리뷰
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code
          # 변경된 파일 목록 추출
          CHANGED=$(git diff --name-only origin/main...HEAD)
          # Claude Code로 리뷰
          claude --print "다음 변경 파일을 리뷰해줘: $CHANGED"`
### 리뷰 체크리스트 자동 생성

CLAUDE.md에 리뷰 기준을 정의하면, Claude Code가 그 기준에 따라 리뷰합니다.

`# CLAUDE.md에 추가
 
## PR 리뷰 체크리스트
PR을 리뷰할 때 다음을 확인합니다:
1. 타입 안전성: TypeScript 타입이 올바른가?
2. 에러 처리: try-catch가 적절한가?
3. 테스트: 새 기능에 테스트가 있는가?
4. 성능: N+1 쿼리, 불필요한 렌더링이 없는가?
5. 보안: 입력 검증, XSS 방지가 되어 있는가?`
---

## PR 워크플로우 표준화

팀에서 일관된 PR 워크플로우를 만듭니다.

### 슬래시 커맨드로 표준화

`# .claude/commands/create-pr.md
 
## PR 생성 워크플로우
 
1. 현재 변경사항 확인 (git diff)
2. 변경 내용 요약 작성
3. PR 제목 생성 (Conventional Commits 형식)
4. PR 본문 생성:
   - 변경 요약
   - 테스트 체크리스트
   - 스크린샷 필요 여부
5. gh pr create 실행`
`# 팀원 누구나 같은 형식으로 PR 생성
claude "/create-pr"`
### 커밋 메시지 표준화

`# .claude/commands/commit.md
 
변경사항을 분석하고 Conventional Commits 형식으로 커밋합니다.
 
형식: <type>(<scope>): <description>
 
type: feat, fix, docs, style, refactor, test, chore
scope: 변경된 모듈명
description: 50자 이내, 현재형, 소문자 시작`
팀원마다 커밋 메시지 스타일이 다른 문제가 사라집니다.

---

## 온보딩: 새 팀원에게 Claude Code 소개하기

새 팀원이 합류했을 때, 최소한의 온보딩으로 바로 사용할 수 있게 만듭니다.

### 온보딩 문서 예시

`# Claude Code 온보딩 가이드
 
## 1. 설치 (5분)
npm install -g @anthropic-ai/claude-code
 
## 2. 프로젝트 이해 (10분)
cd 프로젝트-폴더
claude "CLAUDE.md를 읽고 이 프로젝트를 설명해줘"
 
## 3. 첫 작업 (15분)
claude "이 프로젝트의 폴더 구조를 설명해줘"
claude "package.json의 스크립트 목록을 알려줘"
 
## 4. 팀 규칙 확인 (5분)
claude "/help"  # 사용 가능한 커맨드 확인`
가장 효과적인 온보딩은 “CLAUDE.md를 읽고 이 프로젝트를 설명해줘”입니다. 새 팀원이 코드를 읽기 전에 Claude Code에게 프로젝트 맥락을 먼저 물어보면, 이해 시간이 크게 줄어듭니다.

---

## 7일 과정 총정리

Day 0부터 Day 7까지 함께 온 여정을 돌아봅니다.

`Day 0: 설치와 첫 대화          .......... ✅
Day 1: 대화 잘하기              .......... ✅
Day 2: 프로젝트 컨텍스트        .......... ✅
Day 3: 워크플로우 최적화        .......... ✅
Day 4: 자동화 (Hooks & MCP)     .......... ✅
Day 5: 확장 (커맨드 & 통합)     .......... ✅
Day 6: 에이전트 코딩            .......... ✅
Day 7: 실전 시스템              .......... ✅`
7일 전에는 `claude`를 처음 타이핑했습니다. 오늘은 멀티에이전트 시스템을 분석하고, 프로덕션 배포를 논의하고, 팀 운영 전략까지 다뤘습니다.

### 배운 것들의 연결

`CLAUDE.md (Day 2)
  └→ 에이전트 정의의 기반 (Day 6)
       └→ 멀티에이전트 시스템 (Day 7)

Hooks (Day 4)
  └→ 자동화 파이프라인 (Day 4)
       └→ CI/CD 통합 (Day 7)

MCP (Day 4)
  └→ 외부 서비스 연결 (Day 5)
       └→ 실제 시스템 통합 (Day 7)`
모든 것이 연결되어 있습니다. Day 2의 CLAUDE.md 없이는 Day 7의 멀티에이전트 시스템을 만들 수 없습니다.

---

## 다음 단계

플레이북은 여기서 끝나지만, 여러분의 여정은 계속됩니다.

### 추천 다음 행동

| 순서 | 행동 | 기대 효과 |
| --- | --- | --- |
| 1 | 지금 하고 있는 프로젝트에 CLAUDE.md 작성 | 즉시 생산성 향상 |
| 2 | 반복 작업을 슬래시 커맨드로 자동화 | 매일 30분 절약 |
| 3 | 에이전트 하나 만들어서 실전 투입 | 시스템 사고 시작 |
| 4 | 팀에 Claude Code 도입 제안 | 팀 전체 레벨업 |

### 레퍼런스

- [Claude Code 공식 문서](https://docs.anthropic.com/en/docs/claude-code)

- [Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook)

- [Claude Code GitHub](https://github.com/anthropics/claude-code)

축하합니다. 7일 과정을 모두 완료했습니다. 이제 Claude Code를 자유자재로 활용할 수 있는 기반이 만들어졌습니다. 중요한 것은 도구가 아니라 문제를 해결하려는 의지입니다. Claude Code는 그 의지를 실행으로 바꿔주는 도구일 뿐입니다.

---

## 정리

- CLAUDE.md를 팀 규칙서로 활용하면 팀원 전체가 일관된 규칙으로 작업합니다

- `.claude/settings.json`으로 팀 권한 정책을 통일합니다

- GitHub Actions로 코드 리뷰를 자동화합니다

- 슬래시 커맨드로 PR, 커밋 등 반복 워크플로우를 표준화합니다

- 새 팀원은 “CLAUDE.md를 읽고 설명해줘” 한 마디로 온보딩이 시작됩니다

- 7일 과정의 모든 내용은 서로 연결되어 있으며, CLAUDE.md가 그 출발점입니다

다음: [레퍼런스](/docs/reference)
