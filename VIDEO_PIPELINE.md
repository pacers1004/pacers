# 🔗 멀티채널 영상 자동발행 파이프라인 (2026-06-24 설계)

> **목적**: 완성 영상 1개를 폴더에 넣으면 → 채널별 맞춤 문구로 유튜브·인스타·틱톡 자동 발행.
> **원칙**: ① 비용 최대 무료 ② 1소스 멀티업로드 + 채널별 차별화 ③ **승인 게이트**(막 발행 방지)
> **기존 모델**: 카드뉴스 시나리오(Make 6263718/6270177) — Make HTTP→Placid→Instagram 구조 재활용

---

## 전체 구조

```
[대표님] 완성영상 MP4 → Google Drive "완성영상" 폴더에 넣기
            ↓
[Make] Drive 폴더 감시 (새 파일) → Notion 발행 큐에 행 자동 생성 (상태=문구생성대기)
            ↓
[Make] Claude API(HTTP)로 채널별 문구 생성 → Notion 행 채움 (상태=승인대기)
            ↓
🚦[대표님] Notion에서 문구 검수 → 상태를 "승인됨"으로 변경  ← 안전 게이트
            ↓
[Make] 상태=승인됨 행만 집어서:
   ├─ 유튜브 업로드 (SEO 제목/설명)
   ├─ 인스타 Reels 업로드 (감성 캡션)
   └─ 틱톡 업로드 (날것 캡션 + 사운드)
            ↓
[Make] 발행 완료 → Notion 상태=발행됨 + 발행일 기록
```

---

## 0. 영상 공개 호스팅 — Cloudflare R2 (2026-06-24 셋업 완료 ✅)
- **이유**: 인스타 Reels API는 Google Drive 영상 못 읽음 → 공개 직접 URL 필요. R2 = 무료 10GB + **전송(egress) 0원** + 상업용 OK
- **계정**: bimhara@gmail.com / Account ID `e11246e5ea3066cbb681106d92a2e33d`
- **버킷**: `pacers-videos` (Public Development URL 활성화)
- **공개 URL 베이스**: `https://pub-7c04022291a242b887d041c570b06dcf.r2.dev`
- **업로드 방법 (v1)**: R2 대시보드에 영상 드래그 → `https://pub-7c04...r2.dev/<파일명>` 으로 즉시 공개 접근 (별도 업로드 페이지 불필요)
- **다음(선택)**: 드래그 업로드 페이지 제작 → 업로드 시 Make 큐 자동등록까지 (편의 개선)

## 1. 발행 큐 — Make Data Store (2026-06-24 생성 완료 ✅)
> Notion 큐는 Make가 읽으려면 Notion 연결을 또 해야 해서, Make 내장 Data Store로 발행 큐 운영. Notion은 사람용 기획보드로 유지.
- **Data Store**: `영상 발행 큐` (id 138607, teamId 1964933)
- **필드**: 영상URL / 유튜브제목 / 유튜브설명 / 인스타캡션 / 상태(승인대기·승인·발행됨)
- **연결 현황**: 유튜브 `Pacers YouTube`(connId 8495322) ✅ / 인스타(FB connId 8405750, IG 17841478853004855) ✅ / 틱톡 ⏭️ 보류
- **큐 필드명(영문 키)**: `video_url`(R2 공개URL) / `yt_title` / `yt_desc` / `ig_caption` / `status`(승인대기·승인·발행됨)

## 1-A. 발행 시나리오 — Make scenario 6311230 (현재 OFF, 인스타 검증 완료 ✅ / 유튜브 미완)
- **이름**: `Pacers 영상 자동발행 (YT+IG)` / **스케줄**: 매일 10:00 KST / **현재 비활성(OFF)** — 검증 끝날 때까지 꺼둠
- **저장된 모듈 = 3개** (인스타 파이프라인, ✅ 실전 검증 — 실제 릴스 2회 게시 성공):
  ① `datastore:SearchRecord` (datastore 138607, filter `status text:equal 승인`, limit 1)
  → ② `instagram-business:CreateAReelPost` (conn 8405750, accountId `17841478853004855`, `video_url={{1.data.video_url}}`, `caption={{1.data.ig_caption}}`, `share_to_feed=true`)
  → ③ `datastore:UpdateRecord` (datastore 138607, `key={{1.key}}`, `status=발행됨`)
- **⚠️ 핵심 교훈(다음 세션 필독)**: 검색 출력은 `key` + **`data`** 로 감싸여 있어 필드 참조는 반드시 **`{{1.data.필드명}}`** (예: `{{1.data.video_url}}`). `{{1.video_url}}`로 하면 빈값 → 실패.

### ⏳ 다음 세션 — 유튜브 가지 추가 (미완. 자동화로 막혔던 지점)
- **상태**: UI에서 HTTP+YouTube 모듈을 추가했으나 **시나리오 저장이 안 돼 날아감**. 블루프린트엔 아직 3개 모듈만 있음.
- **⚠️ Make 함정**: 모듈 설정창의 `Save`(모듈 저장) ≠ **시나리오 저장**. 모듈 추가/수정 후 반드시 **에디터 좌하단 💾(시나리오 저장)** 눌러야 영구 반영. 안 누르면 모듈이 사라짐 ← 이번에 이걸로 날아감.
- **추가할 모듈** (③ 뒤 또는 ① 뒤에 직렬로):
  - `HTTP > Download a file` : URL = `{{1.data.video_url}}`
  - `YouTube > Upload a Video` : Connection=`My YouTube connection`(connId 8495322) / Title=`{{1.data.yt_title}}` / File=위 HTTP의 "Download a file" 출력(자동연결됨) / Video Category=Sports / Privacy=**Public** / Description=`{{1.data.yt_desc}}` / made for kids=No
  - **YouTube 모듈은 Make API로 사양 조회 불가(빌트인)** → 반드시 Grid UI에서 추가. UI "+"가 자동클릭에 잘 안 먹음 → 사람이 "+" 한 번 눌러주면 그 다음(검색·필드입력)은 진행됨.
- **추가 후 반드시 💾 시나리오 저장** → `scenarios_run`(API) 또는 UI Run once로 검증 → operation이 **5개**(검색·인스타·발행됨·HTTP·유튜브)면 정상. 3개면 유튜브 안 들어간 것.
- **검증 자산**: R2 테스트 영상 `https://pub-7c04022291a242b887d041c570b06dcf.r2.dev/0624.mp4`, 큐 레코드 key `2cbf29c9c62d`(현재 status=발행됨 → 재테스트 시 `data-store-records_update`로 status=승인 되돌리기). yt_title/yt_desc/ig_caption은 SEO 최적화 완료본이 레코드에 들어있음.
- **순서 정리(검증 후)**: 이상적 순서는 검색→유튜브→인스타→발행됨. 현재는 인스타가 먼저라 무방하나, 원하면 `scenarios_update` 블루프린트로 재정렬.

- **막발행 방지**: status=승인인 행만 발행. 평소엔 status=승인대기로 쌓고, 묶어서 한 번에 승인.

## 1-B. (구) 발행 큐 (Notion) — 사람용 기획보드
- URL: https://app.notion.com/p/c4a12933dbab46d78da0f954f50eb80b (CMO 마케팅 플랜 하위)
- 컬럼: 영상제목 / 상태 / 소스영상URL / 브리프 / 채널 / 유튜브제목 / 유튜브설명 / 인스타캡션 / 틱톡캡션 / 해시태그 / 발행일
- **상태 흐름**: 문구생성대기 → 승인대기 → (대표님)승인됨 → 발행됨 / 보류
- **핵심**: 상태=승인됨인 행만 Make가 발행. 대표님이 검수 안 하면 아무것도 안 나감.

## 2. 채널별 문구 차별화 (유튜브 중복 페널티 회피)
같은 영상이라도 채널마다 다른 메타데이터를 Claude API가 생성:

| 채널 | 제목/캡션 규칙 |
|------|---------------|
| 유튜브 Shorts | 검색 키워드("러닝메이트/혼자 러닝/함께 달리기")를 **제목 첫 3단어**. 40자 이내. 감성카피는 설명란 |
| 인스타 Reels | 감성 캡션 OK. 블라인드 러닝 톤. 해시태그 풀세트 |
| 틱톡 | 첫 줄 강한 후킹 + 날것 느낌. 짧은 해시태그 3~5개 |

**Claude 프롬프트 입력**: 브리프(영상 주제 한줄) → 출력 JSON `{youtube_title, youtube_desc, insta_caption, tiktok_caption, hashtags}`
**금지 규칙**(쿨다운 동일): 자기계발어/격식체/날씨·계절·지명·대회명.
**워터마크 주의**: CapCut에서 **워터마크 없이** clean export (틱톡 워터마크 박힌 영상 유튜브 업로드 금지 — 페널티).

## 3. 🚦 대표님이 직접 하셔야 하는 것 (OAuth — 제가 대신 못 함)
현재 Make 연결: **인스타(페이스북)만 ✅**. 아래 2개 연결 필요:

- [ ] **유튜브**: Make → Connections → YouTube 연결. ⚠️ **반드시 페이서스 채널(@withPacers) 소유 구글 계정**으로 (잘못된 계정 연결 시 엉뚱한 채널에 업로드)
- [ ] **틱톡**: Make → TikTok 연결. ⚠️ TikTok 업로드는 비즈니스 계정 + Content Posting API 승인 필요할 수 있음 — 연결 시 확인. (안 되면 틱톡만 1단계 수동 유지, 유튜브·인스타 먼저 자동화)

## 4. 💰 비용 — 무료 한도 주의 (정직하게)
- Make 무료 = **월 1,000 ops**. 현재 카드뉴스 2개가 이미 약 840 ops/월 소비 추정.
- 영상 파이프라인 추가 시 합산 1,000 초과 가능 → 둘 중 하나:
  - (A) **무료 유지**: Drive 상시감시 대신 **하루 1회 스케줄**로 승인된 행만 체크 (ops 절약) + 영상 발행 빈도 제한
  - (B) Make Core **$9/월** 업그레이드(10,000 ops) — 영상까지 여유롭게
- 권장: 우선 (A)로 무료 검증 → 영상 발행량 늘면 (B).

## 5. 단계별 실행 순서
1. ✅ Notion 발행 큐 생성
2. ⏳ 대표님: 유튜브·틱톡 Make 연결 (위 §3 체크리스트)
3. ⏳ Make 시나리오 구축 (인스타+유튜브 먼저, 틱톡은 연결되면 추가)
4. ⏳ 테스트 영상 1개로 E2E 검증 (큐 등록 → 문구생성 → 승인 → 3채널 발행)
5. ⏳ 검증 완료 → 상시 운영

> **다음 작업**: 대표님이 §3 유튜브/틱톡 연결을 끝내면, 제가 Make 시나리오 블루프린트를 만들어 넣습니다. 연결 전까지는 인스타 단독 발행 버전부터 구축 가능.
