---
title: "프롬프트 레시피"
---
# 프롬프트 레시피

> 상황별로 바로 복사해서 쓸 수 있는 프롬프트 모음입니다.

각 레시피는 **상황 한 줄 + 바로 쓸 수 있는 프롬프트**로 구성했습니다. 그대로 복사하셔도 되고, 본인 상황에 맞게 수정하셔도 됩니다.

> ℹ️ **정보**
>
> 프롬프트 작성의 기본 원칙은 [Day 3 - 프롬프트 잘 쓰는 법](/docs/day-3/prompting)에서 다룹니다. 여기서는 실전 레시피만 모았습니다.

---

## 코드 작성

### 1. 함수 만들기

새로운 유틸리티 함수가 필요할 때.

`src/utils/validation.ts에 이메일 유효성 검사 함수를 만들어줘.
- 함수명: validateEmail
- 입력: string
- 출력: { isValid: boolean, error?: string }
- 정규식으로 기본 형식 검사 + 도메인 유효성까지
- JSDoc 주석 포함`
### 2. API 엔드포인트

REST API 엔드포인트를 만들어야 할 때.

`src/routes/users.ts에 사용자 CRUD API를 만들어줘.
- GET /users — 전체 목록 (페이지네이션: page, limit 쿼리)
- GET /users/:id — 단건 조회
- POST /users — 생성 (이름, 이메일 필수)
- PUT /users/:id — 수정
- DELETE /users/:id — 삭제 (소프트 삭제)
- Express + TypeScript 기준
- 에러 처리 미들웨어 패턴으로`
### 3. React 컴포넌트

UI 컴포넌트를 새로 만들어야 할 때.

`src/components/SearchBar.tsx를 만들어줘.
- props: placeholder, onSearch(query: string), isLoading
- 300ms 디바운스 적용
- Enter 키와 검색 버튼 모두 동작
- 입력 중일 때 X 버튼으로 초기화
- Tailwind CSS로 스타일링`
### 4. 데이터 모델

데이터베이스 스키마나 타입을 정의할 때.

`src/models/order.ts에 주문 데이터 모델을 만들어줘.
- Prisma 스키마 기준
- 필드: id, userId, items(JSON), totalAmount, status(enum: pending/paid/shipped/delivered/cancelled), createdAt, updatedAt
- User와 1:N 관계
- status 변경 이력도 별도 테이블로 추적`
---

## 디버깅

### 5. 에러 분석

에러 메시지가 나왔는데 원인을 모를 때.

`이 에러 분석해줘:

TypeError: Cannot read properties of undefined (reading 'map')
    at UserList (src/components/UserList.tsx:23:18)
    at renderWithHooks (node_modules/react-dom/...)

언제 발생하는지, 왜 발생하는지, 어떻게 고치는지 알려줘.`
### 6. 성능 문제

페이지가 느리거나 API 응답이 늦을 때.

`src/pages/Dashboard.tsx가 렌더링이 너무 느려.
- 현재 상태: 목록 100개 로드 시 3초 걸림
- 원인 분석해주고
- React.memo, useMemo, 가상 스크롤 등 적용 가능한 최적화 방법을 코드와 함께 알려줘`
### 7. 타입 에러

TypeScript 타입 에러가 해결이 안 될 때.

`이 타입 에러 고쳐줘:

Type 'string | undefined' is not assignable to type 'string'.
  Type 'undefined' is not assignable to type 'string'.

src/services/auth.ts:45:3

타입 단언(as) 없이, 안전한 방법으로 해결해줘.`
---

## 리팩토링

### 8. 함수 분리

하나의 함수가 너무 길어졌을 때.

`src/services/orderService.ts의 processOrder 함수가 150줄이야.
- 단일 책임 원칙에 맞게 작은 함수들로 분리해줘
- 각 함수에 명확한 이름 붙여줘
- 기존 동작은 바뀌면 안 돼
- 분리 전후 비교도 간단히 보여줘`
### 9. 네이밍 개선

변수명, 함수명이 불분명할 때.

`src/utils/helpers.ts의 변수명과 함수명을 개선해줘.
- d → date, startDate 등 의미 있는 이름으로
- fn, cb 같은 약어도 풀어줘
- 함수명은 동사+명사 패턴으로 (예: getData → fetchUserProfile)
- 변경 전후 매핑 표도 보여줘`
### 10. 중복 제거

비슷한 코드가 여러 곳에 있을 때.

`src/components 폴더에서 중복 코드를 찾아서 정리해줘.
- 비슷한 로직이 반복되는 곳을 찾아줘
- 공통 훅이나 유틸 함수로 추출해줘
- 기존 컴포넌트들이 추출된 함수를 쓰도록 수정해줘`
---

## 테스트

### 11. 유닛 테스트

특정 함수의 단위 테스트를 만들 때.

`src/utils/calculate.ts의 calculateDiscount 함수에 대한 Jest 테스트를 만들어줘.
- 파일: src/utils/__tests__/calculate.test.ts
- 케이스: 정상 할인, 0% 할인, 100% 할인, 음수 입력, 소수점 처리
- 경계값 테스트 포함
- describe/it 구조로 깔끔하게`
### 12. 통합 테스트

API 엔드포인트를 테스트할 때.

`src/routes/users.ts의 API 통합 테스트를 만들어줘.
- 파일: src/routes/__tests__/users.integration.test.ts
- supertest 사용
- 각 엔드포인트(GET, POST, PUT, DELETE) 테스트
- 인증 필요한 요청은 토큰 포함
- 에러 케이스(404, 400, 401)도 테스트
- 테스트 DB 설정/해제 포함`
### 13. E2E 테스트

사용자 시나리오 전체를 테스트할 때.

`회원가입 → 로그인 → 프로필 수정 흐름의 Playwright E2E 테스트를 만들어줘.
- 파일: e2e/auth-flow.spec.ts
- 회원가입 폼 입력 → 이메일 인증(모킹) → 로그인 → 프로필 수정 → 로그아웃
- 각 단계에서 올바른 페이지로 이동하는지 확인
- 에러 상황(중복 이메일, 잘못된 비밀번호)도 테스트`
---

## 문서화

### 14. README 작성

프로젝트 README를 만들어야 할 때.

`이 프로젝트의 README.md를 만들어줘.
- 프로젝트 한 줄 소개
- 주요 기능 목록
- 기술 스택
- 설치 방법 (단계별)
- 환경변수 설정 (.env.example 기반)
- 실행 방법 (개발/프로덕션)
- 폴더 구조 트리
- 기여 가이드 간단히`
### 15. API 문서

API 명세를 문서화할 때.

`src/routes 폴더의 모든 API 엔드포인트를 문서화해줘.
- 형식: 각 엔드포인트별 METHOD, URL, 설명
- 요청 파라미터/바디 예시
- 응답 예시 (성공 + 에러)
- 인증 필요 여부
- Markdown 표로 깔끔하게`
---

## Git 작업

### 16. 스마트 커밋

변경사항을 의미 있게 커밋할 때.

`변경사항 확인하고 적절한 커밋 메시지로 커밋해줘.
- conventional commits 형식 (feat:, fix:, refactor: 등)
- 한국어 본문, 영어 prefix
- 관련 없는 변경은 별도 커밋으로 분리해줘`
### 17. PR 리뷰

동료의 PR을 리뷰해야 할 때.

`이 PR을 리뷰해줘: https://github.com/team/repo/pull/42
- 코드 품질, 버그 가능성, 성능 이슈 체크
- 개선 제안이 있으면 코드 예시와 함께
- 심각도 표시: 🔴 필수 수정, 🟡 권장, 🟢 사소`
### 18. 변경 분석

최근 변경사항을 파악해야 할 때.

`최근 5개 커밋에서 변경된 내용을 분석해줘.
- 각 커밋별 변경 파일과 핵심 내용
- 전체적으로 어떤 기능이 추가/수정됐는지 요약
- 혹시 위험해 보이는 변경이 있으면 표시`
---

## 프로젝트 관리

### 19. 구조 분석

새 프로젝트에 투입됐을 때.

`이 프로젝트를 전체적으로 분석해줘.
- 어떤 프로젝트인지 한 줄 요약
- 기술 스택 (프레임워크, 라이브러리, DB 등)
- 폴더 구조와 각 폴더 역할
- 진입점 (entry point) 파일
- 주요 설정 파일 설명
- 코드 컨벤션이 보이면 알려줘`
### 20. 의존성 업데이트

패키지를 업데이트해야 할 때.

`이 프로젝트의 npm 의존성 상태를 점검해줘.
- 오래된 패키지 목록
- 보안 취약점 있는 패키지
- 메이저 업데이트가 필요한 것들
- 각각 업데이트 시 주의할 점
- 안전하게 업데이트하는 순서 추천`
---

## 레시피 활용 팁

> ℹ️ **정보**
>
> 이 레시피들을 CLAUDE.md에 자주 쓰는 것들만 모아두면, Claude가 매번 프로젝트 컨텍스트에 맞게 적용해줍니다.

**레시피 커스터마이징 3원칙:**

1. **경로는 구체적으로** — `src/utils/` 같은 실제 경로를 넣으세요

1. **기술 스택 명시** — React, Vue, Express 등 프레임워크를 꼭 적으세요

1. **출력 형태 지정** — “표로”, “코드로”, “목록으로” 등 원하는 형태를 말하세요

`# 나만의 레시피 만드는 공식
[어디서] + [무엇을] + [어떻게] + [제약조건]

예: "src/api/에 사용자 인증 미들웨어를 만들어줘. JWT 기반이고, 만료 시 리프레시 토큰으로 자동 갱신해줘"`
