# 🏃 PACERS — 러닝메이트 매칭 플랫폼

> 멀리, 빨리 뛰는 법보다 오늘도 운동화를 다시 신는 법을 제안합니다.

**국내 버블앱 순위 19위 | 라이브 회원 1,079명 | 목표: 국내 1위 → 세계 1위**

---

## 🚀 처음 시작하는 사람을 위해

### 1단계: 프로젝트 이해
→ **[CLAUDE.md](./CLAUDE.md)** 읽기 (5분)
- 프로젝트 정체성, 기술 스택, Feature 01~03

### 2단계: 최근 작업 파악
→ **[CHANGELOG.md](./CHANGELOG.md)** 보기 (2분)
- 날짜순 작업 이력, 상태 변화

### 3단계: 상세 정보 (필요시)
→ **[docs/](./docs/)** 폴더의 각 Feature 문서

---

## 📚 구조 개요

```
pacers/
├── README.md (← 지금 읽는 이 파일)
├── CLAUDE.md (← 메인 개발 가이드)
├── CHANGELOG.md (← 작업 이력 타임라인)
├── docs/ (← Feature별 상세 문서)
│   ├── feature-01-weather-app.md
│   ├── feature-02-cooldown-scheduler.md
│   └── feature-03-daily-pacer.md
├── src/ (← 코드)
└── assets/ (← 설계, 스크린샷 등)
```

---

## ⚡ 빠른 참고

| 항목 | 위치 |
|------|------|
| **기술 스택** | [CLAUDE.md § 3](./CLAUDE.md#3-기술-스택) |
| **기능 파이프라인** | [CLAUDE.md § 2](./CLAUDE.md#2-기능-파이프라인) |
| **버블 인프라** | [CLAUDE.md § 6](./CLAUDE.md#6-버블-앱-인프라) |
| **최근 작업** | [CHANGELOG.md](./CHANGELOG.md) |
| **Daily Pacer 상세** | [CLAUDE.md § 11](./CLAUDE.md#11-feature-03-daily-pacer-매칭-스코어) |

---

## 🔄 협업 규칙

- **메인 문서**: CLAUDE.md (Single Source of Truth)
- **작업 기록**: 매 세션마다 CHANGELOG.md 상단에 추가
- **웹 동기화**: GitHub push (로컬 ↔ 원격)
- **메모리**: Claude Code가 자동 로드 (`~/.claude/`)

---

## 📖 CLAUDE.md 읽는 법

1. **1~5절**: 프로젝트 정체성 + 기술
2. **6~10절**: 인프라 + 데이터 구조
3. **11~17절**: Feature별 상세 (작업 이력 포함)
4. **18절+**: 최신 작업 이력

---

**다음: [CLAUDE.md](./CLAUDE.md) 읽기**
