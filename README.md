# VibeWorld

<p align="center">
  <img src="https://img.shields.io/badge/React-19-61DAFB?logo=react" alt="React 19">
  <img src="https://img.shields.io/badge/Vite-8-646CFF?logo=vite" alt="Vite 8">
  <img src="https://img.shields.io/badge/Supabase-Pro-3ECF8E?logo=supabase" alt="Supabase">
  <img src="https://img.shields.io/badge/Cloudflare-Pages-F38020?logo=cloudflare" alt="Cloudflare Pages">
  <img src="https://img.shields.io/badge/i18n-KO_|_EN-6366f1" alt="KO | EN">
  <img src="https://img.shields.io/badge/tests-500_passing-22c55e" alt="500 tests passing">
  <img src="https://img.shields.io/badge/coverage-85%25+-6366f1" alt="85%+ coverage">
</p>

<p align="center">
  <strong>Where Ideas Meet Users — Vibe Coding Plaza</strong><br>
  <sub>커뮤니티 기반 바이브 코딩 앱 쇼케이스 플랫폼 • 한국어/English • 500+ 테스트 • Supabase 백엔드</sub>
</p>

---

## 핵심 가치

VibeWorld는 **바이브 코딩으로 만들어진 앱들이 실제 사용자를 만나는 광장**입니다. 단순한 디렉토리가 아니라, 개발자가 자신의 앱을 등록하고 테스터를 모집하며 커뮤니티와 소통하는 풀사이클 플랫폼입니다.

| 가치 | 설명 |
|------|------|
| **제로 게이트키핑** | 어떤 앱이든 제출 가능. AI 모더레이션 + 관리자 승인으로 품질 유지 |
| **테스터 매칭** | 출시 전 앱이 20명까지 테스터를 모집. TestFlight / Google Play Internal Test 연동 |
| **커뮤니티 지식** | 게시판 + VIBE TIP으로 바이브 코딩 노하우 공유 |
| **글로벌 접근** | 한국어/English 완전 이중언어. SEO 최적화, hreflang, 구조화된 데이터 |

## 핵심 기능

### 앱 갤러리
- 22개 카테고리, 관심사 기반 맞춤 피드
- Today's Pick & Weekly Picks 큐레이션
- 실시간 검색, 정렬 (최신순/조회순/좋아요순)
- **테스터 모집** 필터 — 출시 전 앱만 골라보기

### 앱 등록 & 모더레이션
- **듀얼패스 등록**: 출시된 앱(스토어 링크) + 출시 전 앱(테스터 모집)
- AI 기반 콘텐츠 모더레이션 (DeepSeek V4)
- 관리자 승인 파이프라인
- 제출 횟수 제한 및 레이트 리미팅

### 커뮤니티 게시판
- INQUIRY / VIBE_TIP / APP_REQUEST 카테고리
- 마크다운 지원, 좋아요, 댓글, 조회수
- 글 작성자 사후 수정 가능
- 앱 연결(linked_app)로 게시글에서 바로 테스트 참여

### 개발자 대시보드
- 앱별 테스터 명단 및 이메일 대시보드
- 테스터 지원 알림 (Edge Function)
- 앱 통계 (조회수, 추천수, 저장수)

## 기술 스택 선택 이유

| 기술 | 선택 이유 |
|------|----------|
| **React 19 + Vite 8** | 최신 React 동시성 기능. Vite의 빠른 HMR과 빌드 속도 |
| **Vanilla CSS** | Tailwind 의존성 없이 완전한 스타일 제어. 4개 CSS 파일로 컴포넌트/레이아웃/콘텐츠/반응형 분리 |
| **Supabase** | PostgreSQL + Auth + Storage + Edge Functions 통합. 무료 티어로 50k MAU까지 운영 가능 |
| **Cloudflare Pages** | 전 세계 엣지 배포. 무료 티어 대역폭 충분. GitHub Actions 연동 CI/CD |
| **DeepSeek V4** | Anthropic 호환 API. AI 모더레이션 + 커뮤니티 댓글 시딩 + 앱 설명 재작성 |
| **Vitest + Playwright** | 단위/통합 500개 테스트. E2E 크리티컬 플로우 커버리지 |

## 아키텍처

```
vibeworld/
├── src/
│   ├── components/      # 35+ 리액트 컴포넌트 (각각 테스트 포함)
│   │   └── Board/       # 게시판 컴포넌트 (PostModal 등)
│   ├── pages/           # 15개 페이지 (Gallery, Board, AppDetail, Admin...)
│   ├── contexts/        # React Context (AppContext, i18n)
│   ├── hooks/           # useFocusTrap 등 커스텀 훅
│   ├── utils/           # API 클라이언트, 비즈니스 로직 (35+ 유틸)
│   ├── i18n/            # ko/en 번역 (1000+ 키)
│   ├── constants/       # 카테고리, 제한값, 블록리스트
│   └── styles/          # 4개 CSS 파일 (components, content, layout, responsive)
├── supabase/
│   ├── functions/       # 13개 Edge Functions (TypeScript)
│   │   └── _shared/     # 공유 JWT 인증, AI 재작성 핸들러
│   ├── migrations/      # 48개 DB 마이그레이션
│   └── config.toml
├── playwright.config.js # E2E 테스트
├── vite.config.js       # Vite + env 주입 + Vitest 설정
└── justfile             # 작업 자동화 (dev, test, lint, build, deploy)
```

### 데이터 흐름

```
[사용자] → Cloudflare Pages (React SPA)
                ↓
         Supabase REST API (PostgREST)
                ↓
         PostgreSQL + RLS (Row Level Security)
                ↓
    ┌──────────┼──────────┐
    │          │          │
[Storage]  [Auth]  [Edge Functions]
(WebP      (JWT)   (AI 모더레이션,
 이미지)            알림, 시드)
```

### 보안 경계

- **RLS (Row Level Security)**: 모든 테이블에 적용. `auth.uid()` 기반 접근 제어
- **Edge Function JWT 검증**: `_shared/auth.ts` fail-closed. 검증 실패 = 요청 거부
- **PostgREST 메타문자 필터링**: 검색어에서 `( ) * : . " % \` 제거
- **Advisory Lock**: 레이트 리미팅 동시성 제어
- **HTTPS 전용**: `isHttpsPublicUrl()`로 외부 URL 검증
- **CSP + 보안 헤더**: `_headers` 파일로 Cloudflare Pages에서 적용

## 기술적 도전과 해결

### 1. WebP 클라이언트 변환 + Supabase Storage 업로드

**도전**: 사용자가 어떤 크기/형식의 이미지를 업로드하더라도 일관된 형식으로 저장하고, 무료 티어 범위 내에서 운영해야 함.

**해결**:
- Canvas API로 클라이언트 측 WebP 변환 (1200px max width, quality 0.82, 5MB limit)
- `URL.createObjectURL` / `revokeObjectURL` 라이프사이클로 메모리 누수 방지
- Supabase Storage bucket에 `{userId}/{timestamp}-{random}.webp` 구조로 저장
- RLS: SELECT는 public, INSERT는 authenticated, DELETE는 owner only

### 2. 테스터 모집 사전 조건 (Prerequisite) 검증

**도전**: 아무나 테스터를 모집하는 것을 방지하면서도, 진입 장벽은 낮게 유지해야 함.

**해결**:
- DB 트리거 `has_tester_participation()`: 다른 앱에 테스터로 먼저 참여해야 자신의 앱 등록 가능
- 사용자 친화적 에러 메시지: `mapAppSubmitError.js`에서 트리거 에러를 i18n 메시지로 매핑
- `testerApi.js`에서 테스터 수 제한 (앱당 20명) + 진행률 바 UI

### 3. 게시판 글 수정 RLS + 품질 게이트

**도전**: 글 작성자가 자신의 글을 수정할 수 있으면서도, VIBE_TIP 카테고리는 품질 기준을 유지해야 함.

**해결**:
- RLS UPDATE policy에 `WITH CHECK` 조건: INQUIRY는 자유 수정, VIBE_TIP은 title ≥5자 + content ≥80자
- `boardApi.js`의 `updatePost`는 `updated_at`을 자동 갱신하고 작성자 프로필을 join하여 반환
- PostDetailPage에서 `isAuthor` 체크로 수정 버튼 노출 제어

### 4. AI 비용 방어 — Community Seed 제한

**도전**: DeepSeek API로 커뮤니티 댓글을 자동 생성할 때 비용 폭증 방지.

**해결**:
- 앱당 lifetime 최대 2개의 AI 댓글 (변경 불가 하드 리미트)
- `communitySeedEligibility.js`에서 사전 검증
- Edge Function 내에서 `check_and_record_rate_limit` advisory lock으로 동시 실행 방지

## FAQ

<details>
<summary><strong>어떤 앱이든 등록할 수 있나요?</strong></summary>
네. 바이브 코딩으로 만든 앱이라면 어떤 플랫폼(Android, iOS, Web)이든 등록 가능합니다. 출시된 앱은 스토어 링크를, 출시 전 앱은 테스트 링크를 제출하면 됩니다.
</details>

<details>
<summary><strong>테스터 모집은 어떻게 시작하나요?</strong></summary>
다른 앱에 먼저 테스터로 참여한 후, 본인 앱 등록 시 "테스터 모집"을 활성화하면 됩니다. 최대 20명까지 모집 가능하며, 테스터 지원자 명단은 개발자 대시보드에서 확인할 수 있습니다.
</details>

<details>
<summary><strong>심사는 얼마나 걸리나요?</strong></summary>
AI 자동 모더레이션을 먼저 통과한 후 관리자 수동 승인 단계를 거칩니다. 보통 24시간 이내에 처리됩니다.
</details>

<details>
<summary><strong>왜 Vanilla CSS를 사용하나요?</strong></summary>
Tailwind 같은 유틸리티 프레임워크 없이도 충분히 생산적인 스타일링이 가능함을 보여주기 위함입니다. 4개 CSS 파일(css/components.css, css/content.css, css/layout.css, css/responsive.css)로 모든 스타일을 관리하며, 커스텀 프로퍼티로 다크/라이트 테마를 지원합니다.
</details>

<details>
<summary><strong>한국어/영어 외 다른 언어를 추가할 수 있나요?</strong></summary>
`src/i18n/translations.js`에 새 언어 키-값 쌍을 추가하면 됩니다. 현재 1000개 이상의 번역 키가 구조화되어 있어 확장이 용이합니다.
</details>

<details>
<summary><strong>셀프 호스팅이 가능한가요?</strong></summary>
네. Supabase 프로젝트 + Cloudflare Pages 계정만 있으면 전체 스택을 자체 운영할 수 있습니다. `.env.example`을 참고하여 환경 변수를 설정하고 `npm run build && wrangler pages deploy`로 배포하면 됩니다.
</details>

---

<p align="center">
  <sub>Built with vibe coding • Powered by Supabase & Cloudflare • <a href="https://vibeworld.sharebible.org">Live Site</a> • <a href="mailto:musicbox26@gmail.com">Contact</a></sub>
</p>
