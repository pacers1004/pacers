# PACERS 개발 이력

> **원칙**: 매 세션마다 이 파일 상단에 작업 내용을 추가합니다.  
> **형식**: 날짜 + 주요 작업 + 상태 + 링크

---

## 📅 2026-06-19 | Claude Code (admin@pacers.kr)

### 🎯 주요 작업

**GitHub 저장소 생성 및 문서 동기화 프로토콜 확립**
- pacers1004/pacers 저장소 생성 ✅
- README + CLAUDE + CHANGELOG + ONBOARDING 4개 문서 push ✅
- **새 프로토콜**: Context 90%에서 자동으로 대표님 승인 요청
- 다음 Claude Code는 이 규칙을 자동 인지하고 따름

**세션 끝 프로토콜 정의**
- 모든 Claude Code가 따를 표준 프로세스 문서화
- CLAUDE.md § 19에 명시 (embedded rule)
- work-log.md에 rule 추가
- **핵심 개선**: Context 80% 도달 시 자동으로 새 세션 권장
- [상세](./CLAUDE.md#-세션-끝-프로토콜)

**비개발자 관점 설명**
- PACERS 현황, GitHub, CI/CD, 앞으로의 워크플로우 설명
- 문서 동기화는 "반자동" (나가 물어봄, 대표님이 승인)
- Feature 배포는 "자동" (Vercel, cron-job.org)

### 📊 현재 상태

| 항목 | 상태 |
|------|------|
| GitHub 저장소 | ✅ 생성 & 푸시 완료 |
| 문서 통합 | ✅ 4개 파일 동기화 |
| 세션 끝 프로토콜 | ✅ 규칙 embedded |
| 다음 Claude 온보딩 | ✅ 9분 자동화 |

---

## 📅 2026-06-18 | Claude Code (admin@pacers.kr)

### 🎯 주요 작업

**1대1 호감(Daily Card) 딥링크 전략 수립 (절충안)**
- 원래: BN Push 딥링크 직진 → 문제: 스플래시 멈춤
- 최종: **모든 푸시를 알림탭으로 통일** (안정성 ↑)
- [상세](./CLAUDE.md#2026-06-18)

**채팅 푸시 전략 확정**
- Plan B: 채팅 목록으로 진입 (`?tab=chatting`) ✓

**모든 푸시 알림탭 통합 전략**
- 쿨다운 (기존): BN Push 딥링크 유지
- Daily Card, 채팅, 오픈런: 알림탭으로 통합
- 수정 패턴: Launch URL만 변경
- [상세](./CLAUDE.md#모든-푸시-알림탭-통일-전략)

**1대1 호감 메커니즘 상세 분석**
- RunningInvite 생성 → Notification 자동 포함
- 핵심: 알림탭 진입 시 current_invite 자동 설정
- [상세](./CLAUDE.md#1대1-호감-메커니즘-상세-분석)

**테스트 환경 문제 발견**
- iPhone 1개에서 여러 계정 푸시 중복
- 원인: 같은 폰 = 같은 subscription_id
- 결론: 테스트 오염, **라이브 무영향** ✓

**라이브 서비스 마일스톤**
- 회원: **1,079명 달성** 🎉
- 긍정적: 탈퇴율 개선 (마라톤 + 오픈런 효과)

### 📊 현재 상태

| 항목 | 상태 |
|------|------|
| Daily Card 딥링크 | ✅ 절충안 확정 |
| 채팅 푸시 | ✅ Plan B 성공 |
| 모든 푸시 통합 | ✅ 전략 수립 |
| OneSignal 오염 | ✅ 원인 파악 (라이브 안전) |
| 라이브 회원 | ✅ 1,079명 |

### 📝 기록

- CLAUDE.md 업데이트 (§18 추가)
- work-log.md 업데이트 (2026-06-18)
- 문서 구조 통합 (README + CHANGELOG + CLAUDE)

---

## 📅 2026-06-17 | Claude Code (admin@pacers.kr) + bimhara@gmail.com

### 🎯 주요 작업

**쿨다운 댓글 푸시 알림 딥링크**
- OneSignal → BN Push Notification 전환 ✅
- iOS/Android 모두 작동 확인 ✅

**쿨다운 스케줄러 전환**
- GitHub Actions → cron-job.org (9개 정시 잡) ✅
- 무료 플랜 17시간 지연 문제 해결 ✅

**매칭 스코어 라이브 배포**
- 2026-06-16 라이브 완료 ✅

**Claude Code 환경 세팅**
- Bash 권한 추가 ✓
- 플러그인 연결 ✓

### 📊 현재 상태

| Feature | 상태 |
|---------|------|
| 01_Weather | ✅ 라이브 |
| 02_Cooldown | ✅ 라이브 (스케줄러 전환) |
| 03_DailyPacer | ✅ 라이브 (매칭 스코어) |

---

## 📖 읽기 순서

1. **[README.md](./README.md)** ← 진입점
2. **[CLAUDE.md](./CLAUDE.md)** ← 메인 문서
3. **[이 파일](./CHANGELOG.md)** ← 타임라인
4. **[docs/](./docs/)** ← 상세 (필요시)

---

**마지막 업데이트**: 2026-06-18 by Claude Code (admin@pacers.kr)
