---
title: "Headless — 사람 없이 실행"
---
# Headless — 사람 없이 실행

## 오늘의 목표

> Claude Code를 사람의 개입 없이 자동으로 실행하기

지금까지는 터미널에서 Claude와 대화하며 작업했습니다. 하지만 매번 사람이 앉아서 지시할 필요가 없는 작업도 있습니다. Headless 모드를 배우면 Claude를 **자동화 파이프라인**에 넣을 수 있습니다.

---

## Headless란?

리모컨으로 조종하는 로봇을 상상해보세요. 평소에는 사람이 옆에서 “이거 해, 저거 해”라고 말하지만, 리모컨에 프로그램을 넣어두면 **사람이 지켜보지 않아도 알아서 일합니다**.

Headless 모드가 바로 그겁니다. 대화형 인터페이스 없이, **명령 하나로 Claude에게 작업을 시키고 결과를 받는 것**입니다.

`# 일반 모드 (대화형)
claude
 
# Headless 모드 (비대화형)
claude -p "할 일을 적어주세요"`
`-p` 플래그가 핵심입니다. “이 프롬프트를 실행하고 결과만 돌려줘”라는 뜻입니다.

---

## 기본 사용법

### 가장 간단한 형태

`claude -p "src 폴더의 파일 목록을 알려줘"`
이 명령을 실행하면 Claude가 작업을 수행하고, 결과를 터미널에 출력한 뒤 **자동으로 종료**됩니다. 대화창이 열리지 않습니다.

### JSON으로 결과 받기

스크립트에서 결과를 파싱하려면 JSON 형식이 편합니다.

`claude -p "package.json의 dependencies를 분석해줘" --output-format json`
출력이 JSON으로 나오므로 다른 프로그램에서 읽기 쉽습니다.

### 파일 입력과 함께 사용하기

파이프로 입력을 전달할 수도 있습니다.

`cat src/utils.js | claude -p "이 코드를 리뷰해줘"`
Headless 모드에서는 Claude가 권한을 물어보지 않습니다. 모든 **읽기 작업**은 자동으로 허용됩니다. 쓰기/실행 작업에 대해서는 `--allowedTools` 플래그로 제어할 수 있습니다.

---

## 실습 1: 스크립트에서 Claude 호출하기

간단한 쉘 스크립트로 자동 코드 요약기를 만들어봅시다.

**Step 1**: `summarize.sh` 파일을 만듭니다.

`#!/bin/bash
# 지정한 파일의 코드를 요약합니다
 
FILE=$1
 
if [ -z "$FILE" ]; then
  echo "사용법: ./summarize.sh <파일경로>"
  exit 1
fi
 
claude -p "다음 파일을 분석해서 3줄로 요약해줘: $FILE"`
**Step 2**: 실행 권한을 줍니다.

`chmod +x summarize.sh`
**Step 3**: 실행해봅니다.

`./summarize.sh src/index.js`
사람이 아무것도 입력하지 않아도 Claude가 파일을 분석하고 요약을 출력합니다.

---

## 실습 2: 여러 파일 한번에 처리하기 (배치)

실무에서는 파일 하나가 아니라 **여러 파일을 한꺼번에** 처리해야 할 때가 많습니다.

`#!/bin/bash
# src 폴더의 모든 .js 파일에 대해 코드 리뷰를 실행합니다
 
for file in src/*.js; do
  echo "===== $file 리뷰 중 ====="
  claude -p "다음 파일을 리뷰해줘. 문제가 있으면 알려줘: $file"
  echo ""
done`
이 스크립트를 실행하면 src 폴더의 모든 JavaScript 파일을 순서대로 리뷰합니다.

파일이 많으면 시간과 API 비용이 많이 들 수 있습니다. 처음에는 2-3개 파일로 테스트하고, 결과가 만족스러우면 범위를 넓히세요.

---

## CI/CD에서 활용하기

Headless 모드의 진가는 **CI/CD 파이프라인**에서 발휘됩니다. 대표적인 예가 GitHub Actions에서 자동 코드 리뷰입니다.

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
 
      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code
 
      - name: Run Code Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "이 PR의 변경된 파일을 리뷰해줘. \
            보안 이슈, 성능 문제, 코드 스타일을 체크해줘" \
            --output-format json > review.json`
PR이 올라올 때마다 Claude가 자동으로 리뷰합니다. 사람이 잠든 새벽에도 동작합니다.

CI/CD 환경에서는 `ANTHROPIC_API_KEY` 환경 변수만 설정되어 있으면 Claude Code가 인증을 자동으로 처리합니다. GitHub Secrets에 API 키를 저장해두세요.

---

## 주의사항: 권한 자동 승인의 위험

대화형 모드에서는 Claude가 “이 파일을 수정해도 될까요?”라고 물어봅니다. 하지만 Headless 모드에서는 **물어보지 않을 수 있습니다**.

### 안전하게 사용하는 원칙

`# 안전한 사용 예시: 읽기 도구만 허용
claude -p "코드를 분석해줘" --allowedTools "Read,Grep,Glob"`
| 원칙 | 설명 |
| --- | --- |
| **읽기만 허용** | 처음에는 파일 읽기/분석만 시키세요 |
| **허용 도구 제한** | `--allowedTools`로 사용 가능한 도구를 명시하세요 |
| **테스트 환경 먼저** | 프로덕션 코드에 바로 적용하지 마세요 |
| **결과 검토** | 자동 수정 결과는 반드시 사람이 확인하세요 |

Headless 모드에서 파일 수정을 허용하면 의도치 않은 변경이 생길 수 있습니다. 프로덕션 환경에서는 반드시 **읽기 전용**으로 시작하세요.

**규칙**: 결과가 예측 가능하고 패턴이 반복되는 작업(코드 리뷰, 로그 분석)에 Headless가 좋습니다. 창의적 판단이 필요한 작업은 대화형 모드가 낫습니다.

---

## 정리

- Headless 모드는 `claude -p "프롬프트"` 형태로 사람 없이 실행합니다

- `--output-format json`으로 프로그래밍에 적합한 출력을 받을 수 있습니다

- 쉘 스크립트와 결합하면 배치 처리가 가능합니다

- CI/CD 파이프라인(GitHub Actions 등)에서 자동 코드 리뷰에 활용됩니다

- **권한 관리에 주의**하세요. 처음에는 읽기 전용으로 시작하는 것이 안전합니다

다음은 코드에서 Claude를 직접 호출하는 Agent SDK를 배웁니다.

> [다음: Agent SDK — 코드로 제어 →](/docs/day-6/agent-sdk)
