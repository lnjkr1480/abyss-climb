# QUAD // ABYSS CLIMB — 배포 가이드

## 1단계. Supabase 프로젝트 만들기 (전 세계 온라인 리더보드용, 무료)

1. https://supabase.com → **Start your project** → GitHub 계정으로 가입/로그인
2. **New project** 클릭 → 프로젝트 이름(아무거나), DB 비밀번호(자동생성 추천), 리전은 `Northeast Asia (Seoul)` 선택 → **Create new project** (1~2분 소요)
3. 왼쪽 메뉴 **SQL Editor** → **New query** → 아래 SQL 붙여넣고 **Run**:

```sql
create table scores (
  name text primary key,
  score int not null,
  updated_at timestamptz not null default now()
);

alter table scores enable row level security;

create policy "public read" on scores
  for select using (true);

create policy "public insert" on scores
  for insert with check (true);

create policy "public update" on scores
  for update using (true);
```

4. 왼쪽 메뉴 **Project Settings > API** 로 이동 → 다음 두 값을 복사:
   - **Project URL** (예: `https://xxxxxxxx.supabase.co`)
   - **anon public** key (긴 문자열) — 이 키는 브라우저에 노출돼도 안전하도록 설계된 키입니다. 위의 RLS 정책이 실제 접근 권한을 통제합니다.

5. `index.html` 파일을 열어서 상단부의 아래 두 줄을 방금 복사한 값으로 교체:

```js
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

이 값을 채워 넣기 전까지는 게임은 정상 작동하지만 리더보드는 비어있는 상태로만 뜹니다 (에러 없이 조용히 비활성).

## 2단계. GitHub에 올리기

```bash
cd abyss-climb
git init
git add .
git commit -m "Initial commit: Abyss Climb"
git branch -M main
git remote add origin https://github.com/<본인계정>/abyss-climb.git
git push -u origin main
```
(GitHub에 먼저 `abyss-climb`라는 이름의 빈 저장소를 만들어두세요: https://github.com/new)

## 3단계. Vercel로 배포 (자동배포 포함)

1. https://vercel.com → GitHub 계정으로 로그인
2. **Add New... > Project** → 방금 만든 `abyss-climb` 저장소 Import
3. Framework Preset은 **Other**로 두고, 그대로 **Deploy** 클릭 (빌드 설정 필요 없음 — 정적 HTML 한 장)
4. 몇 초 뒤 `https://abyss-climb-xxxx.vercel.app` 같은 URL이 발급됩니다. 이 링크를 전 세계 누구에게나 공유하면 폰/PC 어디서든 접속해서 플레이할 수 있어요.

### 이후 업데이트 방법
코드를 수정한 뒤:
```bash
git add .
git commit -m "설명"
git push
```
푸시할 때마다 Vercel이 자동으로 재배포합니다 (수동 배포 명령 필요 없음).

## 참고: 원래 파일과 달라진 점
- 닉네임 저장: Claude 아티팩트 전용 `window.storage` → 기기별 `localStorage`로 변경 (기기마다 마지막 닉네임 기억)
- 점수 리더보드: `window.storage` → Supabase 테이블로 변경 (전 세계 유저가 공유하는 진짜 온라인 랭킹)
- 그 외 게임 로직, 디자인, 사운드는 전혀 건드리지 않았습니다.
