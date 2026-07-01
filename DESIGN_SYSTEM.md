# PACERS 디자인 시스템

---

## 1. 브랜드 철학

**앱명**: 페이서스 (Pacers)  
**슬로건**: "함께 달리는 시작, 페이서스"  
**무드 키워드**: 감각적, 힙, 따뜻한 커뮤니티, 절제된 고급스러움

### 핵심 철학
- "컬러가 감성을 만드는 게 아니라, 여백과 타이포그래피가 감성을 만든다"
- "덜 쓸수록 더 고급스럽다"
- "모션은 장식이 아니라 의미를 전달하는 언어다"

### 레퍼런스 앱/브랜드
| 브랜드 | 참고 요소 |
|--------|----------|
| Nike Run Club | 흑백 대비, 타이포 중심, 러닝 중 다크 전환 |
| Apple Fitness+ | 스크롤 트리거 모션, 풀블리드 비주얼 |
| Strava | 데이터 시각화 밀도 |
| Linear.app | 마이크로인터랙션, 트랜지션 |

---

## 2. 컬러 시스템

### 라이트 모드 — 기본 (일상/탐색 화면)

```
Background:      #FFFFFF
Surface:         #F5F5F5
Surface Raised:  #EEEEEE
Text Primary:    #111111
Text Secondary:  #757575
Text Disabled:   #BBBBBB
Border Subtle:   #E5E5E5
Border Default:  #CCCCCC
```

### 다크 모드 — 러닝 액티브 화면 전용

```
Background:      #000000
Surface:         #111111
Text Primary:    #FFFFFF
Text Secondary:  rgba(255,255,255,0.60)
Text Tertiary:   rgba(255,255,255,0.36)
Border:          rgba(255,255,255,0.10)
```

### 브랜드 컬러

```
Pacers Blue: #3764F1
```

**사용 원칙**:
- 전체 화면에서 최대 1~2군데에만 등장
- Primary CTA 버튼 1개, 활성 탭 아이콘
- 배경 전체에 깔지 말 것
- 컬러로 위계 만들지 말 것 → 크기와 굵기로 만들 것

### Semantic

```
Success: #22C55E
Warning: #F59E0B
Error:   #EF4444
```

### 화면별 모드 지정

**라이트 적용 화면** (흰 배경 + 검정 텍스트):
- 홈 피드, 프로필, 마라톤 캘린더
- 쿨다운(커뮤니티), 매거진, 설정
- Daily Crew 목록, Open Run 목록, 매칭 대기

**다크 적용 화면** (검정 배경 + 흰 텍스트):
- 스플래시, 온보딩
- 러닝 진행 중 라이브 화면
- 러닝 완료 결과 화면 (임팩트 강조)
- 매칭 성사 순간 화면

---

## 3. 타이포그래피

> 철학: 크기 차이 = 위계. 컬러 차이로 위계 만들지 않음.

| 토큰 | 크기 | 굵기 | tracking | 용도 |
|------|------|------|----------|------|
| Hero Stat | 80~96px | 800 | -0.03em | 거리, 페이스, 시간 숫자. 화면의 주인공. |
| Display | 48px | 700 | -0.02em | 결과 화면 타이틀, 온보딩 임팩트 문구 |
| Headline | 28px | 700 | -0.01em | 섹션 제목, 카드 그룹 타이틀 |
| Title | 20px | 600 | — | 카드 제목, 모달 타이틀 |
| Body | 16px | 400 | — | 본문, 설명 텍스트 (line-height 1.6) |
| Label | 14px | 600 | — | 버튼, 탭, 배지 |
| Caption | 12px | 500 | 0.08em | 보조 정보, 날짜, 카테고리 (UPPERCASE) |

### 모션과 타이포 연계
- **Hero Stat**: 항상 count-up 애니메이션으로 등장
- **Display**: Reveal(clip-path) 방식으로 등장
- **Headline**: fade + Y축 상승으로 진입

---

## 4. 모션 시스템 (핵심)

> 철학: 모션은 "보여주기"가 아니라 "상태 변화를 설명하는 것". 빠르게, 목적 있게, 과하지 않게.

### 이징 — 아래 4개만 사용

```css
--ease-spring:      cubic-bezier(0.16, 1, 0.3, 1);      /* 기본 진입. 자연스럽게 튀어오름 */
--ease-smooth:      cubic-bezier(0.4, 0, 0.2, 1);        /* 화면 전환, 모달 슬라이드 */
--ease-snappy:      cubic-bezier(0.34, 1.56, 0.64, 1);   /* 버튼 탭 피드백. 살짝 오버슈트 */
--ease-decelerate:  cubic-bezier(0, 0, 0.2, 1);          /* 아래에서 올라오는 요소, 드로어 */
```

`linear` → 무한 루프, 로딩 shimmer 전용

### 듀레이션

| 토큰 | 범위 | 용도 |
|------|------|------|
| Instant | 100~150ms | 탭 피드백, 토글, 체크 |
| Fast | 200~250ms | 툴팁, 드롭다운, 소형 모달 |
| Normal | 350~400ms | 화면 전환, 모달 등장 |
| Slow | 550~650ms | 첫 진입 히어로, stagger 완료 |
| Dramatic | 800~1000ms | 스플래시, 러닝 완료 결과, 특별 강조 |

### 진입 원칙

**기본 요소 진입**:
```css
transform: translateY(32px) → translateY(0);
opacity: 0 → 1;
duration: 400ms; easing: Spring;
```

**리스트/카드 stagger**:
- 각 아이템 70ms 간격 순차 등장
- 최대 5개까지만 stagger (이후 동시)

**숫자 (거리/페이스/시간)**:
- count-up 애니메이션
- duration 800ms, Decelerate easing
- 시작값: 0 또는 목표값의 60%부터

**텍스트 Reveal**:
```css
clip-path: inset(0 100% 0 0) → inset(0 0% 0 0);
duration: 600ms; easing: Spring;
/* Display급 타이틀에만 사용 */
```

### 자주 쓰는 패턴

**Pulse** (대기 상태 CTA):
```css
animation: scale 1 → 1.06 → 1;
duration: 1800ms; easing: linear; iteration: infinite;
/* 러닝 시작 버튼, 매칭 대기 버튼 */
```

**Shimmer** (스켈레톤 로딩):
```css
background: linear-gradient(90deg, #EEE 25%, #F8F8F8 50%, #EEE 75%);
background-size: 200% 100%;
animation: slide left→right, 1500ms linear infinite;
/* spinner 절대 사용 금지. 항상 skeleton으로. */
```

**Flash** (탭 피드백):
```css
opacity: 1 → 0.6 → 1;
duration: 150ms; easing: Instant;
/* 모든 터치 가능 요소에 적용 */
```

**Page Transition** (화면 전환):
```
앞으로: 새 화면 X +30px → 0, fade in, 350ms Smooth
뒤로:   현재 화면 X +30px, fade out, 350ms Smooth
```

**Dark Flash** (러닝 시작 순간):
```
화면 전체 #000000으로 순간 전환 200ms → 러닝 화면 fade in 300ms
```

**Modal / Bottom Sheet**:
```css
transform: translateY(100%) → translateY(0); duration: 400ms; Decelerate;
overlay opacity: 0 → 0.6; duration: 300ms;
```

**Achievement / 결과 화면**:
```
1. 숫자 count-up 800ms
2. 하단 상세 정보 stagger 등장
3. 배경: 검정 / 숫자만 크게 / 텍스트 최소
```

---

## 5. 컴포넌트 규격

### 버튼

| 종류 | 배경 | 텍스트 | 높이 | radius |
|------|------|--------|------|--------|
| Primary (라이트) | #111111 | white | 56px | 28px |
| Primary (다크) | #FFFFFF | black | 56px | 28px |
| Brand CTA | #3764F1 | white | 56px | 28px |
| Ghost | transparent | #111 or white | 56px | 28px |

**탭 피드백**: `scale 0.96, 150ms, Snappy`  
Brand CTA는 가장 중요한 액션 딱 1개에만 사용

### 카드 (라이트)
```css
background: #FFFFFF;
border: 1px solid #E5E5E5;
border-radius: 16px;
padding: 20px;
/* 그림자 없음. border로 구분. */
```
진입: `Y +32px → 0, opacity 0→1, Spring easing`

### 카드 (다크)
```css
background: #111111;
border: 1px solid rgba(255,255,255,0.10);
border-radius: 16px;
padding: 20px;
```

### 인풋
```css
height: 52px;
background: #F5F5F5;          /* 다크: #111111 */
border-radius: 12px;
border: 1px solid #E5E5E5;    /* 다크: rgba(255,255,255,0.12) */
/* focus */
border-color: #3764F1;
```

### 탭바
```css
height: 80px;   /* safe area 포함 */
background: rgba(255,255,255,0.92); backdrop-filter: blur(20px); /* 라이트 */
background: rgba(0,0,0,0.92);       backdrop-filter: blur(20px); /* 다크 */
/* 아이콘: 24px / active #3764F1 / inactive #BBBBBB */
```

### 스켈레톤 로딩
```css
background: #EEEEEE;   /* 다크: #1A1A1A */
/* shimmer: linear-gradient 슬라이드 */
/* border-radius: 컴포넌트와 동일 */
```

---

## 6. 레이아웃 원칙

```
화면 좌우 여백:  24px  (절대 줄이지 말 것)
섹션 상하 간격:  40px
카드 사이 간격:  12px
헤더 높이:       56px
```

**여백 철학**: 빈 공간은 낭비가 아니라 고급스러움. 한 화면에 핵심 정보 1~2개.

**풀블리드**:
- 이미지는 좌우 여백 없이 화면 끝까지
- 텍스트는 이미지 위 오버레이 (gradient 없이 흰 or 검정 텍스트)

**구분선**:
```css
/* 라이트 */ border: 1px solid #E5E5E5;
/* 다크 */   border: 1px solid rgba(255,255,255,0.08);
/* 그림자/그라데이션으로 구분하지 말 것 */
```

---

## 7. 화면별 모션 명세

### 스플래시 (다크)
1. 검정 배경
2. 페이서스 로고: `scale 0.8→1.0, opacity 0→1, 600ms Spring`
3. 500ms 대기
4. 흰 선이 화면을 수평으로 가르며 전환: `clip-path: inset(50% 0 50% 0) → inset(0 0 0 0), 400ms`

### 온보딩 (다크)
- 각 슬라이드: 텍스트 Reveal + 이미지 fade
- 페이지 전환: 수평 slide, 350ms Smooth
- CTA: 흰 버튼 + Pulse 애니메이션 (대기 중)

### 홈 피드 (라이트)
- 첫 진입: 헤더 fade in → 카드 stagger 70ms 간격
- 스크롤 시: 헤더 backdrop-blur 0→20px 강화
- 새로고침: pull-to-refresh 시 #3764F1 spinner (유일하게 허용)

### 매칭 대기 → 성사 (라이트→다크 전환)
- 대기 중: 라이트 배경, Pulse 버튼
- 매칭 성사: Dark Flash → 검정 배경으로 전환
- 상대방 정보 등장: 위에서 아래로 cascade, stagger 100ms

### 러닝 진행 중 (다크)
- Hero Stat(거리): 화면 중앙, 80px, count-up
- 화면 스와이프: 페이스/심박/지도 전환, 350ms Smooth
- 지도 버튼: 탭 시 immersive 애니메이션으로 확장
- 일시정지: 버튼 scale down 0.96, 150ms

### 러닝 완료 결과 (다크)
1. 검정 배경 유지
2. 거리 숫자 count-up, 800ms Dramatic
3. 하단 상세 (페이스/시간/칼로리) stagger 100ms 간격 등장
4. 공유/저장 버튼 fade in, 600ms 후
5. 축하 haptic + 미세한 파티클 효과 (선택)

### 마라톤 캘린더 (라이트)
- 달력 날짜 탭: `scale 1→1.1→1, 200ms Snappy`
- 대회 카드 펼치기: height 애니메이션 + 내용 fade
- 스크롤: 상단 헤더 parallax (이미지 0.5x 속도)

---

## 8. 레퍼런스 사이트 목록

Claude Code는 아래 URL을 fetch해서 패턴 분석 후 적용할 것.

### 앱 UI 레퍼런스
| URL | 참고 내용 |
|-----|---------|
| https://mobbin.com | "Nike Run Club" 검색: 흑백 대비 / "Strava": 데이터 시각화 / "Apple Fitness": 풀블리드+모션 |
| https://60fps.design | Nike Training Club 모션 패턴 영상, 마이크로인터랙션 분석 |
| https://www.awwwards.com/sites/ | 스크롤 트리거, 페이지 전환 수상작 패턴 |
| https://linear.app | 마이크로인터랙션 교과서. 버튼/호버/전환 전반 |
| https://www.apple.com/kr/airpods-pro/ | 스크롤 트리거 애니메이션 패턴 |
| https://stripe.com | 섹션 진입 모션, 타이포 애니메이션 |

### 모션 구현 레퍼런스
| URL | 참고 내용 |
|-----|---------|
| https://easings.net | 이징 커브 시각화 확인 |
| https://gsap.com/docs/v3/ | 웹 모션 구현 (ScrollTrigger 포함) |
| https://codepen.io/GreenSock | GSAP 실제 예제 코드 |
| https://www.framer.com/motion/ | Next.js/React 모션 구현 |
| https://docs.swmansion.com/react-native-reanimated/ | React Native 모션 구현 |

### 디자인 시스템 참고
| URL | 참고 내용 |
|-----|---------|
| https://lapa.ninja | 흑백 랜딩페이지 레이아웃 패턴 |
| https://www.ibm.com/design/language/animation/overview/ | 모션 원칙 — 언제 쓰고 언제 안 쓰는지 |

---

## 9. Bubble.io 적용 가이드

- **컬러 토큰**: Styles 탭에 섹션 2 값으로 등록
- **단순 fade/slide**: Bubble 기본 애니메이션 활용
- **복잡한 모션**: HTML Element + CSS animation / GSAP
- **숫자 count-up**: HTML Element + JavaScript
- **Skeleton 로딩**: HTML Element + CSS shimmer

**BDK/Natively Native 최적화**:
```css
overflow: hidden;                           /* 전체 적용 */
-webkit-overflow-scrolling: touch;
translate3d() 사용 (transform3d 대신);     /* GPU 가속 */
will-change: transform;                     /* 모션 요소에만 */
```

**다크↔라이트 전환**:
- 화면 단위로 완전히 다른 페이지로 처리 (Bubble)
- 또는 조건부 그룹 visible/hidden 토글

---

## 10. 개발 원칙 (Claude Code 행동 지침)

### 반드시 지킬 것
- 모든 탭/클릭에 반드시 피드백 모션 (최소 Flash 150ms)
- 로딩은 무조건 Skeleton UI (spinner 사용 금지)
- 빈 상태(Empty State)도 디자인: 아이콘 + 짧은 문구
- 에러는 인라인으로 (팝업 알림 최소화)
- 버튼 최소 48px (러닝 중 손가락 정확도 고려)
- 한 화면 핵심 정보 1~2개

### 절대 하지 말 것
- 배경에 그라데이션 깔기
- 여러 컬러 동시 사용
- 카드에 `box-shadow` 추가
- 애니메이션을 "장식"으로 사용
- 화면 가득 정보 채우기
- `#3764F1`을 3군데 이상 사용
