---
title: "Docker로 안전하게"
---
# Docker로 안전하게

## 오늘의 목표

> Docker 환경에서 Claude Code를 안전하게 실행하기

Day 6의 마지막 주제입니다. “Claude가 내 컴퓨터에서 마음대로 돌아다니면 어떡하지?”라는 걱정, Docker가 해결해줍니다.

---

## Docker를 왜 쓰는가?

실험실에서 위험한 화학 실험을 할 때 **보호 장갑**을 끼고, **방호 유리** 안에서 작업합니다. 실험이 잘못되어도 실험자는 안전하죠.

Docker는 소프트웨어의 보호 장갑입니다. Claude Code를 Docker 컨테이너 안에서 실행하면, Claude가 아무리 실수해도 **내 컴퓨터의 진짜 파일에는 영향이 없습니다**.

`Docker 없이:
  Claude Code → 내 컴퓨터 파일 시스템 (전부 접근 가능)

Docker 안에서:
  Claude Code → 컨테이너 파일 시스템 (격리된 공간)
                     ↓
              컨테이너 삭제하면 흔적도 없음`
### Docker를 쓰면 좋은 상황

| 상황 | 이유 |
| --- | --- |
| Headless 모드로 자동 실행 | 사람이 안 볼 때 실수를 방지 |
| 서브에이전트 병렬 실행 | 각 에이전트를 격리된 환경에서 실행 |
| CI/CD 파이프라인 | 빌드 환경의 일관성 보장 |
| 낯선 코드 분석 | 신뢰하기 어려운 코드를 안전하게 분석 |

---

## Docker 기본 개념 (30초 정리)

Docker를 처음 접한다면 이것만 알면 됩니다.

| 용어 | 비유 | 설명 |
| --- | --- | --- |
| **이미지** | 설계도 | ”이런 환경을 만들어라”는 레시피 |
| **컨테이너** | 실제 방 | 이미지로 만든 격리된 실행 공간 |
| **Dockerfile** | 레시피 문서 | 이미지를 만드는 명령어 모음 |

이미지(설계도)로 컨테이너(실제 방)를 만들고, 그 방 안에서 작업합니다. 방을 부숴도 집(내 컴퓨터)은 멀쩡합니다.

---

## 기본 Dockerfile 예제

Claude Code를 실행할 Docker 이미지를 만들어봅시다.

`FROM node:20-slim
RUN npm install -g @anthropic-ai/claude-code
WORKDIR /workspace
COPY . /workspace
# API 키는 환경 변수로 실행 시점에 전달 (여기에 직접 넣지 마세요!)`
**절대로** Dockerfile이나 코드에 API 키를 직접 쓰지 마세요. 환경 변수로 실행 시점에 전달합니다. 키가 코드에 들어가면 Git에 올라가서 유출될 수 있습니다.

---

## Docker에서 Claude Code 실행하기

**Step 1**: 이미지를 빌드합니다.

`docker build -t claude-workspace .`
**Step 2**: 컨테이너를 실행합니다.

`docker run --rm \
  -e ANTHROPIC_API_KEY="sk-ant-..." \
  claude-workspace \
  claude -p "이 프로젝트를 분석해줘" --output-format json`
각 옵션의 의미입니다.

| 옵션 | 설명 |
| --- | --- |
| `--rm` | 실행 끝나면 컨테이너 자동 삭제 |
| `-e` | 환경 변수 전달 (API 키) |
| `claude-workspace` | 사용할 이미지 이름 |
| `claude -p "..."` | 실행할 명령어 |

### 로컬 폴더를 마운트해서 사용하기

프로젝트 파일을 매번 복사하지 않고, 로컬 폴더를 직접 연결할 수도 있습니다.

`docker run --rm \
  -e ANTHROPIC_API_KEY="sk-ant-..." \
  -v $(pwd):/workspace \
  claude-workspace \
  claude -p "src 폴더를 리뷰해줘"`
`-v $(pwd):/workspace`가 현재 폴더를 컨테이너의 `/workspace`에 연결합니다. 읽기 전용으로 하고 싶다면 `:ro`를 붙입니다.

`-v $(pwd):/workspace:ro    # 읽기 전용 마운트`
읽기 전용 마운트(`:ro`)를 쓰면 Claude가 파일을 수정할 수 없습니다. 코드 분석이나 리뷰처럼 읽기만 필요한 작업에 가장 안전한 방법입니다.

---

## CI/CD 파이프라인 통합

Docker와 Headless 모드를 결합하면 완전 자동화된 파이프라인이 만들어집니다.

`# .github/workflows/claude-docker-review.yml
name: Claude Docker Review
 
on:
  pull_request:
    types: [opened, synchronize]
 
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
 
      - name: Run Claude in Docker
        run: |
          docker run --rm \
            -e ANTHROPIC_API_KEY=${{ secrets.ANTHROPIC_API_KEY }} \
            -v ${{ github.workspace }}:/workspace:ro \
            node:20-slim \
            bash -c "
              npm install -g @anthropic-ai/claude-code &&
              cd /workspace &&
              claude -p '변경된 파일을 리뷰해줘' --output-format json
            "`
이 워크플로우는 PR이 올라올 때마다 Docker 안에서 Claude가 코드 리뷰를 합니다. 읽기 전용 마운트이므로 코드가 수정될 일은 없습니다.

---

## 보안 모범 사례

| 원칙 | 방법 |
| --- | --- |
| **API 키 보호** | 환경 변수로만 전달. Dockerfile에 직접 넣지 마세요 |
| **네트워크 격리** | `docker run --network none`으로 외부 접근 차단 |
| **리소스 제한** | `--memory 2g --cpus 1.0`으로 과도한 사용 방지 |
| **일반 사용자 실행** | Dockerfile에 `USER claudeuser` 추가 |

보안의 핵심 원칙은 **최소 권한**입니다. Claude에게 필요한 만큼만 허용하고, 나머지는 차단합니다.

Day 1~5처럼 대화하며 작업하는 경우 Docker는 불필요합니다. 하지만 **사람이 보지 않는 자동화 상황**(CI/CD, Headless, 서브에이전트)에서는 Docker가 안전장치 역할을 합니다.

---

## 정리

- Docker는 Claude Code를 **격리된 환경**에서 안전하게 실행하는 방법입니다

- 컨테이너 안에서 실수해도 내 컴퓨터에는 영향이 없습니다

- API 키는 반드시 **환경 변수**로 전달합니다 (코드에 넣지 마세요)

- 읽기 전용 마운트(`:ro`)로 파일 수정을 원천 차단할 수 있습니다

- CI/CD 파이프라인에서 Docker + Headless 조합이 가장 안전합니다

- **최소 권한 원칙**을 항상 기억하세요

---

## Day 6 완료!

에이전트 세계에 입문했습니다. 오늘 배운 것을 돌아봅시다.

| 주제 | 핵심 한 줄 |
| --- | --- |
| **Skills** | Claude에게 상황별 전문 매뉴얼을 주는 것 |
| **Headless** | 사람 없이 자동으로 Claude를 실행하는 것 |
| **Agent SDK** | 코드 안에서 Claude를 호출하는 것 |
| **서브에이전트** | 여러 Claude가 팀으로 협업하는 것 |
| **Docker** | 이 모든 것을 안전하게 감싸는 것 |

이 5가지를 조합하면 **나만의 AI 에이전트 시스템**을 만들 수 있습니다. Day 7에서 실전 프로젝트를 완성합시다!

> [다음: Day 7 — 실전 프로젝트 →]({{ '/docs/day-7.html' | relative_url }})
