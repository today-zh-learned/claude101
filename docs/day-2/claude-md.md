---
title: "CLAUDE.md 만들기"
---
# CLAUDE.md 만들기

## 오늘의 목표

> CLAUDE.md 파일로 Claude에게 내 프로젝트를 알려줍니다.

어제까지 Claude Code와 대화하고, 파일을 읽고 쓰는 법을 배웠습니다. 오늘부터는 **프로젝트 단위**로 Claude를 활용하는 법을 익힙니다.

---

## CLAUDE.md가 뭔가요?

새 팀원이 회사에 입사하면 **온보딩 문서**를 받습니다. “우리 팀은 이런 일을 하고, 이런 규칙이 있고, 이건 이렇게 해주세요” 같은 내용이죠.

CLAUDE.md는 **Claude에게 주는 온보딩 문서**입니다.

이 파일을 프로젝트 폴더 최상단에 놓아두면, Claude Code가 대화를 시작할 때 **자동으로 읽습니다**. 매번 “이 프로젝트는 이러이러한 거야”라고 설명할 필요가 없어집니다.

> ℹ️ **정보**
>
> CLAUDE.md가 없어도 Claude Code는 동작합니다. 하지만 매번 프로젝트 설명을 반복해야 하고, Claude가 프로젝트 맥락 없이 답변하게 됩니다. CLAUDE.md 하나면 이 문제가 해결됩니다.

---

## /init으로 자동 생성하기

가장 쉬운 방법부터 해봅시다. Claude Code 안에서 `/init`을 입력하면 됩니다.

`> /init`
Claude가 프로젝트 폴더의 파일들을 분석해서 CLAUDE.md 초안을 자동으로 만들어줍니다. 기술 스택, 폴더 구조, 주요 파일 등을 알아서 파악합니다.

자동 생성된 내용이 완벽하진 않습니다. 하지만 빈 문서에서 시작하는 것보다 훨씬 빠릅니다. 초안을 받고 나서 수정하면 됩니다.

---

## 좋은 CLAUDE.md의 구성

좋은 CLAUDE.md에는 이런 내용이 들어갑니다.

### 1. 프로젝트 개요

`## Project Overview
쇼핑몰 관리 웹 애플리케이션입니다.
상품 등록, 주문 관리, 매출 통계를 처리합니다.`
“이 프로젝트가 뭘 하는 건지” 한두 줄이면 충분합니다.

### 2. 기술 스택

`## Tech Stack
- Frontend: React + TypeScript
- Backend: Node.js + Express
- Database: PostgreSQL
- 패키지 매니저: pnpm`
Claude가 코드를 작성할 때 어떤 기술을 써야 하는지 알게 됩니다.

### 3. 프로젝트 규칙

`## Rules
- 함수명은 camelCase로 작성합니다
- 컴포넌트 파일은 PascalCase로 작성합니다
- API 응답은 항상 { success, data, error } 형태입니다
- 테스트 없이 커밋하지 않습니다`
이 부분이 핵심입니다. 규칙을 명시하면 Claude가 **일관된 스타일**로 코드를 작성합니다.

### 4. 주요 명령어

`## Commands
- 개발 서버: `pnpm dev`
- 테스트: `pnpm test`
- 빌드: `pnpm build``
Claude가 “이거 실행해볼게요”라고 할 때, 올바른 명령어를 쓰게 됩니다.

---

## 나쁜 CLAUDE.md vs 좋은 CLAUDE.md

차이를 비교해봅시다.

**나쁜 예시** — 너무 막연합니다:

`# My Project
웹사이트입니다. React 씁니다.`
Claude는 이걸 읽고도 거의 아무것도 모릅니다. React 버전이 뭔지, 상태관리는 뭘 쓰는지, 폴더 구조가 어떤지 전혀 모릅니다.

**좋은 예시** — 구체적이고 실용적입니다:

`# 쇼핑몰 관리자 대시보드
 
## Overview
소규모 쇼핑몰 운영자를 위한 관리 도구입니다.
상품 CRUD, 주문 상태 관리, 일별 매출 차트를 제공합니다.
 
## Tech Stack
- React 18 + TypeScript
- 상태관리: Zustand
- 스타일: Tailwind CSS
- API: REST (백엔드는 /api 폴더)
 
## Project Structure
- src/components/ — UI 컴포넌트
- src/pages/ — 페이지별 라우트
- src/hooks/ — 커스텀 훅
- src/api/ — API 호출 함수
 
## Rules
- 새 컴포넌트는 src/components/에 만듭니다
- API 함수는 반드시 try-catch로 감쌉니다
- 한국어 주석을 사용합니다
 
## Commands
- 개발 서버: pnpm dev (port 3000)
- 테스트: pnpm test`
> ℹ️ **정보**
>
> 완벽할 필요 없습니다. “Claude가 이걸 읽고 우리 프로젝트에서 바로 일할 수 있을까?”를 기준으로 작성하면 됩니다. 나중에 필요할 때마다 추가하면 됩니다.

---

## 실습: 내 프로젝트에 CLAUDE.md 만들기

직접 해봅시다. 연습 프로젝트가 없다면 아무 폴더나 하나 만들어도 됩니다.

**Step 1**: 터미널에서 프로젝트 폴더로 이동합니다.

`cd ~/my-project`
**Step 2**: Claude Code를 실행합니다.

`claude`
**Step 3**: `/init`을 입력합니다.

`> /init`
**Step 4**: 생성된 CLAUDE.md를 확인합니다.

`> CLAUDE.md 내용 보여줘`
**Step 5**: 부족한 내용이 있으면 Claude에게 추가를 요청합니다.

`> CLAUDE.md에 "컴포넌트는 항상 TypeScript로 작성한다"는 규칙 추가해줘`
이렇게 하면 CLAUDE.md가 완성됩니다. 다음에 Claude Code를 열 때부터 이 내용을 자동으로 읽습니다.

---

## AI싱크클럽 실전 사례

참고로, AI싱크클럽의 youtube_maker 프로젝트에는 이런 CLAUDE.md가 있습니다 (발췌):

`# CLAUDE.md
 
## Project Overview
멀티 채널 유튜브 콘텐츠 제작 자동화 시스템
23개 전문 에이전트가 협업하여 영상 기획부터 후처리까지 자동화
 
## 채널 선택 (Channel Routing)
모든 작업 시작 전 반드시 어떤 채널 작업인지 확인해야 합니다.
 
## Agent 수정 규칙
1. .claude/agents/[에이전트명].md 수정
2. 버전 번호 증가 (v1.0 → v1.1)
3. history.md에 변경 내역 추가`
23개 에이전트가 일관되게 동작하려면, CLAUDE.md에 규칙을 명확히 적어야 합니다. 프로젝트가 커질수록 CLAUDE.md의 가치가 높아집니다.

---

## 정리

- CLAUDE.md는 **Claude에게 주는 프로젝트 온보딩 문서**입니다

- `/init` 명령어로 초안을 자동 생성할 수 있습니다

- 프로젝트 개요, 기술 스택, 규칙, 명령어를 적으면 됩니다

- 완벽하지 않아도 됩니다. 필요할 때마다 추가하면 됩니다

- CLAUDE.md가 있으면 Claude가 **매번 맥락을 파악**하므로 대화가 훨씬 효율적입니다

다음은 Git으로 안전하게 코드를 관리하는 법을 배웁니다.

-> [Git으로 협업하기](/docs/day-2/git-workflow)
