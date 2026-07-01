# 📦 버전 백업 레지스트리

> **규칙**: 라이브 iframe 미니앱(html)을 **크게 바꾸기 전에**, 현재 파일을 `weather-app/archive/<이름>_v<N>.html` 로 복사한다.
> → 이전 버전이 URL로 남아 개선 전/후를 눈으로 비교 가능. (git 히스토리는 브라우저로 못 봄)

## 작업 절차 (Claude Code가 따름)
1. 라이브 html 수정 직전: `cp <name>.html archive/<name>_v<N>.html`
2. `<name>.html` 을 새 버전으로 수정
3. 이 파일(VERSIONS.md) 표에 한 줄 추가 (날짜·URL·변경요약)
4. `git add -A && git push` → Vercel이 archive 포함 전부 배포
5. 다음 개선 땐 N+1

> ⚠️ archive 파일은 **시각 비교용**. 내부 페이지 이동 링크(예: cat → cat_detail)는 상대경로라 archive 안에서 깨질 수 있음 — 모양 확인 용도로만.

---

## 페이서냥 (다마고치) — cat.html / cat_detail.html

| 버전 | 날짜 | URL | 변경 요약 |
|------|------|-----|----------|
| v1 | 2026-06-24 | [/archive/cat_v1.html](https://weather-app-pied-theta.vercel.app/archive/cat_v1.html) · [detail](https://weather-app-pied-theta.vercel.app/archive/cat_detail_v1.html) | SVG 라인아트 고양이 + km 입력 바텀시트 + localStorage failsafe 가드. (이모지→SVG 전환 후 첫 안정 버전) |

## 마라톤 훈련 플랜 — marathon-plan.html

| 버전 | 날짜 | URL | 변경 요약 |
|------|------|-----|----------|
| v1 | 2026-06-24 | [/archive/marathon-plan_v1.html](https://weather-app-pied-theta.vercel.app/archive/marathon-plan_v1.html) | 빈상태/히어로카드/주간캘린더/전체플랜. 버블 미연동, marathon.js NaN 버그(≤2주) 미수정 상태의 스냅샷 |
| v2 | 2026-07-01 | [/archive/marathon-plan_v2.html](https://weather-app-pied-theta.vercel.app/archive/marathon-plan_v2.html) | NRC 화이트 재설계 전 스냅샷 (다크테마 776줄 원본). Task 1 골격 작업 직전 아카이브. |

---

## 라이브(최신) URL — 항상 여기가 현재
- 페이서냥 카드: `https://weather-app-pied-theta.vercel.app/cat.html`
- 페이서냥 상세: `https://weather-app-pied-theta.vercel.app/cat_detail.html`
- 마라톤 플랜: `https://weather-app-pied-theta.vercel.app/marathon-plan.html`
