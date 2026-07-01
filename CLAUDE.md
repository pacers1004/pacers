# PACERS 마스터 문서 (슬림 버전)

> 상세 문서는 `docs/` 폴더 참조. 이 파일은 모든 세션에 자동 로드되므로 핵심만 유지.

---

## 1. 프로젝트 정체성

- **서비스명**: 페이서스(Pacers) — 러닝메이트 매칭 + 소셜 러닝 플랫폼
- **철학**: "오늘도 운동화를 다시 신는 법을 제안합니다"
- **현재**: 국내 버블앱 순위 19위, 유저 1,125명 (2026-06-29 기준)
- **목표**: 국내 1위 → 세계 1위 러닝 플랫폼
- **역할**: 너는 페이서스의 수석 개발자. 능동적으로 제안하고 창의적으로 사고한다.
- **대표님**: 존댓말 사용 필수 (반말 금지)

---

## 2. 기능 파이프라인

| # | 기능명 | 로컬 | 상태 |
|---|--------|------|------|
| 01 | Today Weather | `~/weather-app` | ✅ 라이브 |
| 02 | Cooldown Auto Post | `~/weather-app/api/cooldown.js` | ✅ 라이브 (4회/일: 08·12·18·21시 KST) |
| 03 | Daily Pacer 매칭 스코어 | `~/weather-app/api/match.js` | ✅ 라이브 |
| 04 | 자동 모니터링 & Slack | `~/weather-app/api/monitor.js` | ✅ 라이브 |
| 05 | Daily Pacer 첫인상 | 버블 전용 | ✅ 라이브 |
| 06 | 마라톤 훈련 플랜 | `~/weather-app/marathon-plan.html` | ⚠️ 미완성 (7월 목표) |
| 07 | 페이서 다마고치 | `~/weather-app/cat*.html` | ⚠️ 진행 중 — **블로커: 오버레이 분리** |
| 08 | 구독 모델 | 버블 전용 | 🔜 7월말~8월초 |

---

## 3. 기술 스택 & 개발 환경

- **메인**: Bubble.io (Web) + Natively (앱스토어, BDK에서 이주 중)
- **커스텀 기능**: HTML/JS 미니앱 → Vercel → 버블 iframe 삽입
- **배포**: `git push origin main` → Vercel 자동 배포
- **Vercel 배포 URL**: `https://weather-app-pied-theta.vercel.app`
- **Vercel CLI** (Claude Code Bash): `~/.npm-global/bin/vercel --prod`
- **계정**: `bimhara-9117` / 프로젝트: `pacers-projects1004/weather-app`

---

## 4. 핵심 원칙

### 🚫 최우선: 할루시네이션 금지
추측·상상을 사실처럼 말하지 않는다. 팩트/추측/모름 명확히 구분.

### Auto Mode 작업 기준
| 구분 | 처리 |
|------|------|
| 일반 작업 (파일수정, git push, Vercel 배포, JSON 업데이트) | 바로 진행 |
| 위험한 작업 (DB 삭제, 대규모 변경, 되돌리기 어려운 것) | **반드시 확인 후** |
| 방향 결정 (기능 추가 여부, 전략 결정) | 대표님께 여쭤봄 |

### Failsafe First (모든 iframe 미니앱 필수)
1. 버블 HTML 엘리먼트에 로딩 오버레이 + 5초 타임아웃 재시도(최대 3회)
2. 미니앱 내부에서 `postMessage({ type: '[앱명]_loaded' })` 전송
3. Vercel `stale-if-error` CDN 캐시

### 기타
- API KEY는 환경변수만. 코드 하드코딩 절대 금지.
- 버블 워크로드 폭탄 방지: 불필요한 Search 최소화, User 필드 비정규화 우선.
- UI/디자인: 명시적 요청 없으면 건드리지 않는다.

---

## 5. 디자인 (핵심만)

- 배경: 순수 블랙 `#000000` / 텍스트: 순수 화이트 / 포인트: `#3764F1` only
- NRC 원칙: 숫자를 hero 크기로, 라벨은 작게 아래에
- 상세: [`DESIGN.md`](./DESIGN.md), [`DESIGN_SYSTEM.md`](./DESIGN_SYSTEM.md)

---

## 6. 버블 핵심 인프라

- **도메인**: `pacers.kr`
- **Workflow API**: `https://pacers.kr/version-test/api/1.1/wf`
- **Data API (live)**: `https://pacers.kr/api/1.1/obj/`
- **SPA**: `01_main` 단일 페이지, `?tab=` 파라미터로 탭 전환
- **상세**: [`docs/bubble-infra.md`](./docs/bubble-infra.md)

---

## 7. 현재 작업 상태 & 우선순위

### 🔴 최우선: 다마고치 블로커
`cat_detail.html` 스크롤+오버레이 충돌 → `cat_evo.html` 생성 + 버블 진화 Popup 분리
→ **상세**: [`docs/tamagotchi.md`](./docs/tamagotchi.md)

### 이번 세션 TODO
1. 🔴 cat_evo.html 생성 + cat_detail 2100px 스크롤 + 버블 진화 Popup 배선
2. 🟠 status/last_run_date 연결 (src에 `last_run_date=` 추가)
3. 🟡 내 순위 버그 (`&me=[Current User's unique id]` 추가)
4. 🟡 보안 가드 (1일 1회 티켓 + 거리 상한)
5. 🟢 api/ranking.js BUBBLE_BASE → live 전환

---

## 8. 상세 문서 맵

| 문서 | 내용 |
|------|------|
| [`docs/tamagotchi.md`](./docs/tamagotchi.md) | 다마고치 전체 (캐릭터, 배선, 블로커, QA) |
| [`docs/bubble-infra.md`](./docs/bubble-infra.md) | 버블 DB, OneSignal, 워크플로우, 딥링크 |
| [`docs/natively-migration.md`](./docs/natively-migration.md) | BDK→Natively 이주 |
| [`docs/features-other.md`](./docs/features-other.md) | 매칭 스코어, 첫인상, 마라톤, 운세카드 |
| [`docs/business.md`](./docs/business.md) | 구독 모델, 수익 구조, 라이브 지표 |
| [`DESIGN.md`](./DESIGN.md) | 컬러/타이포 원칙 |
| [`DESIGN_SYSTEM.md`](./DESIGN_SYSTEM.md) | 토큰, 모션, 컴포넌트 |
| [`MARKETING.md`](./MARKETING.md) | 인스타 자동화, 카드풀, 영상 파이프라인 |
| [`AUDIT_2026-06-24.md`](./AUDIT_2026-06-24.md) | 보안 감사 미처리 항목 (F4/F5/F6/F15) |
| [`VERSIONS.md`](./VERSIONS.md) | 파일 버전 백업 레지스트리 |

---

## 9. Context 80% 도달 시

대표님께 보고 후 `git add -A && git commit -m "..." && git push`
