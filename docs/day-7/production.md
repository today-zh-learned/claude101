---
title: "프로덕션에 배포하기"
---
# 프로덕션에 배포하기

## 오늘의 목표

> AI 에이전트 시스템을 실제 서비스로 운영하기 위한 체크리스트를 갖추기

에이전트를 만들었습니다. 로컬에서 잘 돌아갑니다. 그런데 실제 서비스로 운영하려면 몇 가지를 더 챙겨야 합니다.

“잘 돌아간다”와 “안정적으로 운영된다”는 다른 이야기입니다.

---

## 프로덕션 준비 체크리스트

배포 전에 이 표를 하나씩 확인합니다.

| 영역 | 항목 | 확인 |
| --- | --- | --- |
| **보안** | API 키가 환경변수로 관리되는가 | [ ] |
|  | `.env` 파일이 `.gitignore`에 포함되는가 | [ ] |
|  | 사용자 입력이 검증되는가 | [ ] |
|  | 민감한 데이터가 로그에 노출되지 않는가 | [ ] |
| **안정성** | API 호출 실패 시 재시도 로직이 있는가 | [ ] |
|  | 타임아웃이 설정되어 있는가 | [ ] |
|  | 에러 메시지가 사용자 친화적인가 | [ ] |
| **모니터링** | 에이전트별 실행 로그가 남는가 | [ ] |
|  | API 비용이 추적되는가 | [ ] |
|  | 실패 알림이 설정되어 있는가 | [ ] |
| **스케일링** | 동시 실행 제한이 있는가 | [ ] |
|  | 큐 관리가 되는가 | [ ] |

API 키를 코드에 직접 넣지 마세요. 실수로 GitHub에 올라가면 순식간에 수백만 원이 청구될 수 있습니다. 반드시 환경변수를 사용하세요.

---

## 보안: API 키 관리

가장 먼저 챙길 것은 API 키입니다.

`# 절대 하지 말 것
export ANTHROPIC_API_KEY="sk-ant-xxxxx"  # 셸 히스토리에 남음
 
# 이렇게 하세요
# .env 파일 생성
echo "ANTHROPIC_API_KEY=sk-ant-xxxxx" > .env
 
# .gitignore에 추가
echo ".env" >> .gitignore`
팀 환경에서는 비밀 관리 서비스를 사용합니다.

`# GitHub Secrets (CI/CD용)
gh secret set ANTHROPIC_API_KEY
 
# 1Password CLI (로컬 개발용)
op read "op://Development/Anthropic/api-key"`
---

## 안정성: 에러 핸들링

AI API 호출은 실패할 수 있습니다. 네트워크 문제, Rate Limit, 서버 장애 등 다양한 원인이 있습니다.

### 재시도 로직

`import time
 
def call_with_retry(func, max_retries=3, base_delay=1):
    for attempt in range(max_retries):
        try:
            return func()
        except RateLimitError:
            delay = base_delay * (2 ** attempt)  # 1초, 2초, 4초
            print(f"Rate limit. {delay}초 후 재시도...")
            time.sleep(delay)
        except APIError as e:
            if attempt == max_retries - 1:
                raise
            print(f"API 에러. 재시도 {attempt + 1}/{max_retries}")
    raise Exception("최대 재시도 횟수 초과")`
핵심은 **지수 백오프(Exponential Backoff)** 입니다. 재시도 간격을 점점 늘려서 서버에 부담을 주지 않습니다.

### 타임아웃 설정

`# Claude Code에서 타임아웃 설정
claude --timeout 120000 "대본 써줘"  # 2분 제한`
에이전트가 무한히 실행되는 것을 방지합니다. 작업 유형에 따라 적절한 타임아웃을 설정하세요.

---

## 모니터링: 비용 추적

AI API 비용은 예상보다 빠르게 증가합니다. 반드시 추적해야 합니다.

### 간단한 비용 로깅

`# 실행 로그를 파일로 저장
claude "작업 내용" 2>&1 | tee -a logs/claude-$(date +%Y%m%d).log`
### 비용 알림 설정

Anthropic Console에서 비용 알림을 설정합니다.

`Anthropic Console → Settings → Billing → Usage Alerts

추천 설정:
  - 일일 알림: $10 초과 시
  - 주간 알림: $50 초과 시
  - 월간 알림: $200 초과 시`
처음에는 보수적으로 알림을 설정하세요. 실제 사용 패턴을 파악한 후에 조정하면 됩니다.

---

## Docker로 격리하기

Day 6에서 Docker 환경을 다뤘습니다. 프로덕션에서는 격리가 더욱 중요합니다.

`# Dockerfile
FROM node:20-slim
 
# 필요한 도구만 설치
RUN npm install -g @anthropic-ai/claude-code
 
# 작업 디렉토리 설정
WORKDIR /app
COPY . .
 
# 불필요한 파일 제외
COPY .dockerignore .
 
# 비특권 사용자로 실행
RUN useradd -m agent
USER agent
 
CMD ["claude", "--print", "시작"]`
`# .dockerignore
.env
.git
node_modules
*.log`
### 왜 Docker인가?

| 이점 | 설명 |
| --- | --- |
| **격리** | 에이전트가 호스트 시스템에 영향 못 줌 |
| **재현성** | 어디서든 같은 환경으로 실행 |
| **보안** | 파일 시스템 접근 제한 가능 |
| **스케일링** | 컨테이너 복제로 병렬 처리 |

---

## GitHub Actions CI/CD

코드가 변경될 때 자동으로 에이전트를 테스트하고 배포합니다.

`# .github/workflows/agent-ci.yml
name: Agent CI/CD
 
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
 
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
 
      - name: 에이전트 정의 검증
        run: |
          # 모든 에이전트 파일에 필수 섹션이 있는지 확인
          for file in .claude/agents/*.md; do
            echo "검증: $file"
            grep -q "## 역할" "$file" || echo "WARNING: 역할 섹션 없음"
            grep -q "## 출력" "$file" || echo "WARNING: 출력 섹션 없음"
          done
 
      - name: CLAUDE.md 린트
        run: |
          # CLAUDE.md가 존재하고 비어있지 않은지 확인
          test -s CLAUDE.md || exit 1
          echo "CLAUDE.md 확인 완료"
 
      - name: 에이전트 스모크 테스트
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code
          claude --print "CLAUDE.md를 읽고 에이전트 목록을 출력해줘"`
이 파이프라인은 3가지를 확인합니다.

1. 에이전트 정의 파일에 필수 섹션이 있는지

1. CLAUDE.md가 정상인지

1. 실제로 Claude Code가 시스템을 인식하는지

---

## 실패 대응 전략

프로덕션에서 실패는 “만약”이 아니라 “언제”의 문제입니다.

### 실패 유형별 대응

| 실패 유형 | 증상 | 대응 |
| --- | --- | --- |
| **API Rate Limit** | 429 에러 | 지수 백오프 재시도 |
| **API 서버 장애** | 500 에러 | 알림 + 수동 대기 |
| **컨텍스트 초과** | 긴 응답이 잘림 | 작업을 작은 단위로 분할 |
| **잘못된 출력** | 형식이 안 맞음 | 출력 검증 + 재실행 |
| **무한 루프** | 에이전트가 멈추지 않음 | 타임아웃 강제 종료 |

### 그레이스풀 디그레이드

모든 에이전트가 동시에 실패하는 경우를 대비합니다.

`정상: 5개 에이전트 파이프라인 실행
부분 실패: 실패한 에이전트 건너뛰고 다음 단계 진행
전체 실패: 마지막 성공 상태로 롤백 + 알림`
처음부터 모든 실패 케이스를 대비할 필요는 없습니다. 운영하면서 실제로 발생하는 문제를 하나씩 대응해 나가는 것이 현실적입니다.

---

## 프로덕션 운영 팁

실제 운영에서 얻은 교훈 3가지입니다.

**1. 로그는 많을수록 좋습니다** — 문제가 생겼을 때 로그가 없으면 원인을 못 찾습니다. 에이전트의 입력, 출력, 실행 시간을 모두 기록하세요.

**2. 비용 상한을 반드시 걸어두세요** — 에이전트가 예상치 못한 루프에 빠지면 비용이 급증합니다. API 키에 월간 상한을 설정하세요.

**3. 점진적으로 배포하세요** — 새 에이전트를 추가할 때, 처음에는 수동으로 실행하면서 결과를 확인합니다. 안정적이라고 판단되면 자동화합니다.

---

## 정리

- 프로덕션 배포 전에 보안, 안정성, 모니터링, 스케일링을 체크합니다

- API 키는 반드시 환경변수로 관리하고, `.env`는 절대 커밋하지 않습니다

- 재시도 로직에는 지수 백오프를 적용합니다

- Docker로 에이전트 실행 환경을 격리합니다

- GitHub Actions로 에이전트 변경을 자동 검증합니다

- 실패는 “언제”의 문제이므로, 실패 대응 전략을 미리 준비합니다

다음: [팀으로 함께 쓰기]({{ '/docs/day-7/team-work.html' | relative_url }})
