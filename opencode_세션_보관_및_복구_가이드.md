# opencode 세션 보관(Archive) 및 복구(Restore) 완전 가이드

> **작성 기준일:** 2026-09-27
> **적용 버전:** opencode `1.18.26` (Windows 11, PowerShell 5.1)
> **검증 환경:** `C:\Users\Administrator\.local\share\opencode\opencode.db` (646 MB, SQLite 3.45.3)
> **출처:** opencode 공식 문서(tui / cli / keybinds) + GitHub PR #13961, #43919, #15250 + **본 PC 실측 쿼리 결과**

---

## 목차

1. [TL;DR — 30초 요약](#1-tldr--30초-요약)
2. [opencode 세션 보관이란](#2-opencode-세션-보관이란)
3. [⚠️ 먼저 알아야 할 함정 3가지](#3-️-먼저-알아야-할-함정-3가지)
4. [방법 1 — TUI에서 복구](#4-방법-1--tui에서-복구)
5. [방법 2 — CLI로 복구](#5-방법-2--cli로-복구)
6. [방법 3 — SQLite 직접 조작](#6-방법-3--sqlite-직접-조작)
7. [방법 4 — 내보내기 / 가져오기 우회](#7-방법-4--내보내기--가져오기-우회)
8. [방법 5 — 데스크톱 앱](#8-방법-5--데스크톱-앱)
9. [SQL 레퍼런스](#9-sql-레퍼런스)
10. [PowerShell 자동화 스크립트](#10-powershell-자동화-스크립트)
11. [키바인드 레퍼런스](#11-키바인드-레퍼런스)
12. [트러블슈팅](#12-트러블슈팅)
13. [이 PC 현재 현황 (2026-09-27 실측)](#13-이-pc-현재-현황-2026-09-27-실측)
14. [참고 링크](#14-참고-링크)

---

## 1. TL;DR — 30초 요약

**보관한 세션을 되찾는 가장 빠른 길**

```
/sessions        입력 후 Enter        ← 이것만 누르면 끝
```

그 후 대화 목록에서 **"Archived" 그룹**을 찾고 원하는 세션을 선택하면 **자동으로 보관 해제**됩니다.

**안 되면 (TUI에서 보이지 않을 때)**

```powershell
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
sqlite3 $db "UPDATE session SET time_archived = NULL WHERE id = 'ses_xxxxx';"
# → opencode 완전 종료 후 재시작
```

| 방법 | 난이도 | 속도 | 위험도 |
|---|---|---|---|
| TUI `/sessions` | ★☆☆ | 즉시 | 없음 |
| CLI `opencode -s <id>` | ★☆☆ | 즉시 | 없음 |
| SQLite 직접 | ★★☆ | 1분 | 중간 (백업 필수) |
| export → import | ★★★ | 5분+ | 낮음 (세션 ID 변경됨) |

---

## 2. opencode 세션 보관이란

### 2.1 무엇이 보관되는가

opencode의 모든 대화(세션)는 **로컬 SQLite 데이터베이스**에 저장됩니다. "보관"은 이 세션 레코드의 `time_archived` 컬럼에 **밀리초 타임스탬프를 기록하는 것**입니다.

```sql
-- 보관 전 (활성)
UPDATE 상태: time_archived = NULL

-- 보관 후
time_archived = 1790500549060   -- 2026-09-27 18:15:49.060
```

### 2.2 보관 ≠ 삭제

| | 보관(Archive) | 삭제(Delete) |
|---|---|---|
| 대화 내용 | **유지됨** | **영구 삭제** |
| DB 레코드 | 유지 | 삭제 |
| 복구 가능 | ✅ 가능 | ❌ 불가 |
| 기본 목록 노출 | ❌ 숨김 | — |
| 메커니즘 | `time_archived` 에 타임스탬프 | 레코드 삭제 |

**대화 내용이 사라지는 일이 아닙니다.** 화면에서 안 보일 뿐, 데이터는 DB에 그대로 있습니다. 이 점이 모든 복구 방법을 가능하게 합니다.

### 2.3 ⚠️ 보관과 헷갈리는 기능: Prompt Stash

opencode에는 이름이 비슷한 별개 기능이 있습니다. **다르므로 혼동하지 마세요.**

| | 세션 보관 (Archive) | Prompt Stash |
|---|---|---|
| 대상 | 대화(세션) 전체 | 입력창에 작성 중이던 **텍스트 조각** |
| 접근 | 좌상단 메뉴 / `/sessions` | 입력창 단축키 |
| 키바인드 | (공식 스키마에 없음 — §11 참고) | `prompt_stash`, `prompt_stash_pop`, `prompt_stash_list`, `stash_delete` |
| DB | `session.time_archived` | 별도 저장소 |
| 이 문서 범위 | ✅ 포함 | ❌ 제외 |

---

## 3. ⚠️ 먼저 알아야 할 함정 3가지

### 함정 1 — "보관 취소" 버튼이 바로 안 보일 수 있다

버전에 따라 UI가 다릅니다. 이 문서를 작성한 시점(`1.18.26`) 기준입니다.

- **세션 목록에서 아카이브된 항목을 "선택"하면 자동 unarchive** 됩니다 (PR #13961에서 추가)
- 목록 하단에 **"Archived" 그룹**으로 분리 표시됩니다
- `tab` 으로 활성/보관 목록을 전환할 수 있습니다 (버전에 따라)
- 목록 하단 상태 표시줄의 액션 라벨이 `archive` / `unarchive` 로 바뀝니다 — **그 라벨이 지금 눌러야 할 키를 알려줍니다**

> **판별법:** 목록 하단에 표시되는 라벨이权威입니다. `unarchive` 라벨이 보인다는 것은 그 세션이 보관 상태라는 뜻이고, 그 키를 누르면 해제됩니다. `archive` 라벨이면 활성 상태입니다.

### 함정 2 — SQLite로 풀었는데 목록에 안 나타나는 경우

**opencode를 완전히 종료하고 재시작해야 합니다.** 실행 중에는 메모리에 캐시된 세션 목록을 사용하므로 DB를 바꿔도 화면에 반영되지 않습니다.

```powershell
# 실행 중이면 완전 종료 (opencode 내부에서 /exit 또는 ctrl+c, ctrl+d)
```

### 함정 3 — `permission` 필드를 같이 초기화하지 마세요

온라인 커뮤니티에는 "보관 시 `permission` 필드가 NULL이 되므로 같이 복원하라"는 글이 있습니다. **본 PC에서 검증한 결과 이건 사실이 아닙니다.**

```
active    249개 중 145개가 permission = NULL   (58%)
archived   43개 중  43개가 permission = NULL  (100%)
```

활성 세션도 **절반 이상이 이미 NULL**입니다. `permission`은 보관 여부와 무관한 별개 상태값이므로, 복구 시 `time_archived` **만** NULL로 바꾸면 됩니다. `permission`까지 건드리면 오히려 세션 권한 설정이 깨질 수 있습니다.

---

## 4. 방법 1 — TUI에서 복구

### 4.1 세션 목록 열기

어느 방법이든 동일합니다.

| 방법 | 조작 |
|---|---|
| 슬래시 명령 | 입력창에 `/sessions` 입력 후 Enter |
| 별칭 | `/resume` 또는 `/continue` |
| 키바인드 | `ctrl+x` 누른 다음 `l` (leader 키 방식) |

> **leader 키**는 `ctrl+x`가 기본값이며, 다음 키를 누르기까지 **2000ms** 안에 입력해야 합니다. 놓치면 처음부터 다시 눌러야 합니다. `tui.json`의 `leader_timeout`으로 조정 가능합니다.

### 4.2 Archived 그룹 찾기

목록 대화상자가 열리면 상단은 **Pinned / 날짜별 그룹**이고, 하단에 **"Archived" 그룹**이 별도로 나타납니다. 보관된 세션은 여기 모여 있습니다.

```
┌─ 세션 목록 ────────────────────────┐
│ 📌 Pinned                          │
│                                    │
│ 오늘                               │
│   · JEV 환경 설정 및 활용 가이드    │
│   · EMP·CHAMP 전자증폭과 PMT...    │
│                                    │
│ ─ Archived ──────────────────────  │  ← 보관된 세션
│   · 텍스트 파일 마크다운 변환...     │
│   · TurtleBot3 Unity 맵 Quad...    │
│   · Isaac Sim 최신 문서 스크래핑...  │
│                                    │
│ [선택: 텍스트 파일 마크다운 변환]    │
│ ────────────────────────────────  │
│  unarchive  ·  rename  ·  delete   │  ← 이 라벨이 눌러야 할 키를 알려줍니다
└────────────────────────────────────┘
```

### 4.3 복구 동작

| 조작 | 결과 |
|---|---|
| **Archived 세션 선택 (Enter)** | **자동 보관 해제 + 해당 세션으로 이동** |
| 하단 `unarchive` 라벨의 키 | 선택 상태 유지, 보관 해제만 |
| `tab` | 활성 / 보관 목록 전환 (버전에 따라) |
| `ctrl+a` | 아카이브/언아카이브 토글 (버전에 따라) |
| `esc` | 닫기 |

### 4.4 대화 내용 미리 확인

복구 전에 내용이 뭔지 확인하고 싶다면:

| 명령 | 키바인드 | 동작 |
|---|---|---|
| `/sessions` | `ctrl+x l` | 목록에서 **미리보기 패널** 확인 |
| `/export` | `ctrl+x x` | 현재 세션을 **Markdown으로 내보내기** → 기본 에디터에서 열림 |
| `/details` | — | 도구 실행 상세 토글 |

> `/export`는 보관 여부와 상관없이 동작합니다. 복구가 필요 없는 세션이라면 Markdown만 뽑아두고 그대로 두는 것도 방법입니다.

### 4.5 팔레트 검색으로 찾기

```
ctrl+p
```

명령 팔레트에서 **"Archived"** 또는 세션 제목 키워드로 검색하면, 활성 결과와 보관 결과가 **"Archived" 헤딩으로 분리**되어 표시됩니다 (PR #43919).

---

## 5. 방법 2 — CLI로 복구

### 5.1 보관 세션 목록 조회

```powershell
opencode session list
opencode session list -n 20              # 최근 20개
opencode session list --format json      # 기계 판독용
```

> **실측 확인 (v1.18.26):** `opencode session list`는 **보관 세션을 필터링하지 않고 함께 표시합니다.**
> 보관된 `ses_f1dedf62bffeUn3w13LhYeiLsS` ("popular_models_downloader.py 오픈소스 모델 확장")가 목록 최상단에 정상 출력되었습니다.
>
> 즉 **CLI에서는 보관 여부를 구분할 수 없고, 그냥 모든 세션이 나옵니다.** 목록에서 사라진 것처럼 느껴져도 실数据和 CLI 출력은 다를 수 있으므로, 기억에 의존하지 말고 SQL로 확인하세요.

### 5.2 세션 이어서 열기

```powershell
opencode --session ses_f1dedf62bffeUn3w13LhYeiLsS
opencode -s ses_f1dedf62bffeUn3w13LhYeiLsS
```

| 플래그 | 의미 |
|---|---|
| `-c` / `--continue` | 마지막 세션 이어가기 |
| `-s` / `--session` | 지정 세션 ID로 열기 |
| `--fork` | 이어가되 **새 분기 세션** 생성 (원본 보존) |
| `--dir` | 작업 디렉터리 지정 |

> **보관 해제 여부:** 세션을 여는 것만으로 목록에서 복구되지 않을 수 있습니다. 확실히 하려면 §4.1의 TUI에서 한 번 해제하거나, §6의 SQL을 한 번 실행하세요.

### 5.3 데이터베이스 경로 확인

```powershell
opencode db path
# 출력: C:\Users\Administrator\.local\share\opencode\opencode.db
```

다른 OS / 설치 형태에서 경로가 다를 수 있으므로, 직접 탐색하기 전에 이 명령으로 확인하세요.

---

## 6. 방법 3 — SQLite 직접 조작

### 6.1 사전 준비 (건너뛰면 안 됨)

```powershell
# ① opencode 완전 종료 (TUI에서 /exit 또는 ctrl+c 후 ctrl+d)
#    실행 중이면 DB 잠금 및 메모리 캐시로 인해 반영되지 않을 수 있습니다

# ② 백업
$db   = "$env:USERPROFILE\.local\share\opencode\opencode.db"
$bkup = "$env:USERPROFILE\Desktop\opencode-backup-$(Get-Date -Format 'yyyyMMdd-HHmmss').db"
Copy-Item -LiteralPath $db -Destination $bkup
"백업 완료: $bkup"
```

> **본 PC는 DB가 646 MB**입니다. 백업에 시간이 걸리며 디스크 여유 공간을 미리 확보하세요.
> WAL 모드라 `-wal`(60 MB) / `-shm` 파일이 함께 있습니다. **opencode를 종료한 상태에서** 복사해야 일관된 백업이 됩니다.

### 6.2 보관된 세션 조회 (복구 전 필수)

```powershell
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"

sqlite3 -header -column $db @"
SELECT
  id,
  slug,
  datetime(time_archived/1000,'unixepoch','localtime') AS archived_at,
  substr(title,1,50) AS title
FROM session
WHERE time_archived > 0
ORDER BY time_archived DESC;
"@
```

**실제 출력 예시**

```
id                              slug              archived_at          title
------------------------------  ----------------  -------------------  ---------------------------------------
ses_f9defa34fffe78Tcj2ik0bUmxG  eager-island      2026-09-27 18:15:49  텍스트 파일 마크다운 변환 및 분류 정리
ses_f8a3d5053ffeJDH1P2CttLIRCr  cosmic-otter      2026-09-27 18:15:46  TurtleBot3 Unity 맵 Quad 설정 문의
ses_f8e4c5920ffe6ZFvnXu4f8Dzyq  witty-canyon      2026-09-27 18:15:43  Isaac Sim 최신 문서 스크래핑 및 다운로드
```

### 6.3 제목으로 특정 세션 찾기

제목을 기억나지 않을 때 (UTF-8 컬럼이므로 `LIKE` 사용):

```powershell
sqlite3 -header -column $db "SELECT id, title, datetime(time_archived/1000,'unixepoch','localtime') AS at FROM session WHERE time_archived>0 AND title LIKE '%STM32%';"
sqlite3 -header -column $db "SELECT id, title FROM session WHERE time_archived>0 AND title LIKE '%마크다운%';"   # 한글 검색
```

### 6.4 단일 세션 복구

```powershell
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
$id = "ses_f1dedf62bffeUn3w13LhYeiLsS"

sqlite3 $db "UPDATE session SET time_archived = NULL WHERE id = '$id';"
```

**확인**

```powershell
sqlite3 -header -column $db "SELECT id, title, time_archived FROM session WHERE id = '$id';"
# time_archived 가 (빈칸)이면 복구 성공
```

### 6.5 다중 세션 복구

`IN (...)` 으로 여러 개를 한 번에:

```powershell
$db  = "$env:USERPROFILE\.local\share\opencode\opencode.db"
$ids = @("ses_aaa111", "ses_bbb222", "ses_ccc333")
$list = ($ids | ForEach-Object { "'$_'" }) -join ","

sqlite3 $db "UPDATE session SET time_archived = NULL WHERE id IN ($list);"
```

**날짜 조건으로 복구** (특정 날에 잘못 일괄 보관한 경우)

```powershell
# 2026-09-27 18:00~18:30 사이에 보관된 것 전부 되돌리기
sqlite3 $db "UPDATE session SET time_archived = NULL WHERE time_archived BETWEEN 1790500800000 AND 1790502600000;"
```

> 타임스탬프는 **밀리초 단위 Unix epoch**입니다. PowerShell에서 변환:
> ```powershell
> [int64]([DateTimeOffset]::Parse("2026-09-27 18:00:00").ToUnixTimeMilliseconds())
> ```

### 6.6 전체 복구

```powershell
sqlite3 $db "UPDATE session SET time_archived = NULL WHERE time_archived > 0;"

# 결과 확인
sqlite3 $db "SELECT COUNT(*) AS remaining_archived FROM session WHERE time_archived > 0;"
# 0 이면 완료
```

### 6.7 재보관 (되돌리기)

방금 한 작업을 되돌리고 싶을 때:

```powershell
# 특정 세션을 다시 보관
sqlite3 $db "UPDATE session SET time_archived = $([DateTimeOffset]::UtcNow.ToUnixTimeMilliseconds()) WHERE id = 'ses_xxx';"

# 전체 재보관
sqlite3 $db "UPDATE session SET time_archived = $([DateTimeOffset]::UtcNow.ToUnixTimeMilliseconds()) WHERE time_archived IS NULL;"
```

### 6.8 복구 후 반드시

```
1. opencode 완전 종료
2. opencode 재시작
3. /sessions 또는 세션 목록에서 확인
```

---

## 7. 방법 4 — 내보내기 / 가져오기 우회

SQLite를 건드리기 불안정할 때, 또는 **다른 PC로 세션을 옮길** 때 사용합니다.

### 7.1 Linux / macOS (jq 사용)

```bash
opencode export > session-export.json

# archived 필드 제거
jq 'del(.info.time.archived)' session-export.json > session-unarchived.json

# 원본 세션 삭제 후 재import
opencode session delete <sessionID>
opencode import session-unarchived.json
```

### 7.2 Windows PowerShell (jq 없이)

```powershell
$json = Get-Content -Raw -LiteralPath "session-export.json" | ConvertFrom-Json

# info.time.archived 제거
if ($json.info.PSObject.Properties.Name -contains 'time') {
  $json.info.time.PSObject.Properties.Remove('archived')
}

$json | ConvertTo-Json -Depth 100 | Set-Content -Encoding UTF8 "session-unarchived.json"
```

### 7.3 주의

| 항목 | 내용 |
|---|---|
| **세션 ID 변경** | import는 **새 세션 ID를 부여**합니다. 기존 링크·참조가 끊어집니다 |
| 토큰 비용 | import 자체는 모델 호출이 없으므로 **과금되지 않음** |
| 대화 이어가기 | 새 세션이므로 "이어서" 쓸 때는 과거 맥락을 AI가 새로 읽습니다 |
| 목적 | TUI·CLI·SQLite가 모두 실패했을 때 **최후의 수단** |

---

## 8. 방법 5 — 데스크톱 앱

opencode-desktop(데스크톱 앱)를 쓰는 경우, 그래픽 UI로 접근할 수 있습니다.

```
설정(Settings) → 데이터(Data) 탭 → Archived Sessions
```

| 기능 | 설명 |
|---|---|
| 전체 프로젝트 / 현재 프로젝트 필터 | 프로젝트 단위로 보관 세션 추적 |
| 목록에서 직접 보기 | 각 세션의 제목·시각 확인 |
| **Restore (unarchive) 버튼** | 클릭 한 번으로 활성 목록 복귀 |

> 데스크톱 앱을 쓰지 않는다면 이 경로는 무시하세요. **TUI에는 `Archived Sessions` 전용 화면이 없고**, 세션 목록(`/sessions`)의 Archived 그룹이 대체입니다.

---

## 9. SQL 레퍼런스

### 9.1 `session` 테이블 구조 (v1.18.26 실측)

| 컬럼 | 타입 | 설명 |
|---|---|---|
| `id` | TEXT (PK) | 세션 ID (`ses_...`) |
| `project_id` | TEXT | 프로젝트 ID |
| `workspace_id` | TEXT | 워크스페이스 ID |
| `parent_id` | TEXT | 부모 세션 (서브에이전트 세션) |
| `slug` | TEXT | URL 친화적 슬러그 |
| `directory` | TEXT | 작업 디렉터리 |
| `path` | TEXT | 경로 |
| `title` | TEXT | 세션 제목 |
| `version` | TEXT | 생성 시 opencode 버전 |
| `share_url` | TEXT | 공유 URL (공유한 경우) |
| `summary_additions` / `_deletions` / `_files` | INTEGER | 변경 요약 |
| `summary_diffs` | TEXT | diff 요약 |
| `metadata` | TEXT | 메타데이터 |
| `cost` | REAL | 누적 비용 |
| `tokens_input` / `_output` / `_reasoning` / `_cache_read` / `_cache_write` | INTEGER | 토큰 사용량 |
| `revert` | TEXT | revert 스냅샷 |
| `permission` | TEXT | 권한 상태 (**보관과 무관** — §3 함정 3) |
| `agent` | TEXT | 사용 에이전트 |
| `model` | TEXT | 사용 모델 |
| `time_created` | INTEGER | 생성 시각 (ms) |
| `time_updated` | INTEGER | 갱신 시각 (ms) |
| `time_compacting` | INTEGER | 마지막 압축 시각 (ms) |
| **`time_archived`** | **INTEGER** | **보관 시각 (ms). NULL = 활성** |

> ⚠️ **보관 복구에 필요한 컬럼은 `id` 와 `time_archived` 둘뿐입니다.** 나머지는 건드리지 마세요.

### 9.2 자주 쓰는 쿼리 모음

```powershell
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
```

**보관 세션 전체 목록 (최신순)**
```sql
SELECT id, title, datetime(time_archived/1000,'unixepoch','localtime') AS archived_at
FROM session WHERE time_archived > 0
ORDER BY time_archived DESC;
```

**활성 / 보관 개수**
```sql
SELECT CASE WHEN time_archived > 0 THEN 'archived' ELSE 'active' END AS status,
       COUNT(*) AS count
FROM session GROUP BY status;
```

**보관 세션의 프로젝트 분포**
```sql
SELECT directory, COUNT(*) AS n
FROM session WHERE time_archived > 0
GROUP BY directory ORDER BY n DESC;
```

**특정 제목 검색 (한글 포함)**
```sql
SELECT id, title FROM session
WHERE time_archived > 0 AND title LIKE '%검색어%';
```

**오늘 보관된 세션**
```sql
SELECT id, title FROM session
WHERE date(time_archived/1000,'unixepoch','localtime') = date('now','localtime');
```

**보관 상태에서 자식 세션이 있는 것** (서브에이전트 트리 확인)
```sql
SELECT s.id, s.title, COUNT(c.id) AS children
FROM session s
LEFT JOIN session c ON c.parent_id = s.id
WHERE s.time_archived > 0
GROUP BY s.id HAVING children > 0;
```

**백업 없이 세션만 JSON으로 추출** (대화 내용까지 보관)
```powershell
sqlite3 "$env:USERPROFILE\.local\share\opencode\opencode.db" .backup "C:\Users\Administrator\Desktop\opencode-sessions-snapshot.db"
```

### 9.3 실행 후 검증 체크리스트

```powershell
# ① 남은 보관 세션 수
sqlite3 $db "SELECT COUNT(*) FROM session WHERE time_archived > 0;"

# ② 특정 세션 복구 확인
sqlite3 -header -column $db "SELECT id, time_archived, title FROM session WHERE id = 'ses_xxx';"
#    time_archived 가 비어 있어야 복구됨

# ③ DB 무결성
sqlite3 $db "PRAGMA integrity_check;"
#    결과가 "ok" 이어야 정상
```

---

## 10. PowerShell 자동화 스크립트

반복해서 사용할 일이 있다면 아래를 스크립트로 저장하고 호출하세요.

### 10.1 대화형 도구 — `session-archive.ps1`

`C:\Users\Administrator\Desktop\session-archive.ps1` 에 저장:

```powershell
param(
  [ValidateSet('list', 'restore', 'archive', 'purge')]
  [string]$Action = 'list',
  [string]$Id,
  [string]$Like,
  [switch]$All
)

$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
if (-not (Test-Path -LiteralPath $db)) { throw "DB 없음: $db" }

function Show-Archived {
  sqlite3 -header -column $db @"
SELECT id,
       datetime(time_archived/1000,'unixepoch','localtime') AS archived_at,
       substr(title,1,50) AS title
FROM session WHERE time_archived > 0
ORDER BY time_archived DESC;
"@
}

switch ($Action) {
  'list' {
    if ($Like) {
      sqlite3 -header -column $db "SELECT id, title FROM session WHERE time_archived>0 AND title LIKE '%$Like%';"
    } else { Show-Archived }
  }

  'restore' {
    if ($All) {
      $n = sqlite3 $db "UPDATE session SET time_archived = NULL WHERE time_archived > 0; SELECT changes();"
      "전체 복구 완료 (변경 $n 건) → opencode 재시작 필요"
    }
    elseif ($Id) {
      $r = sqlite3 $db "UPDATE session SET time_archived = NULL WHERE id = '$Id'; SELECT changes();"
      if ($r -eq '1') { "복구 완료: $Id → opencode 재시작 필요" }
      else { "해당 세션 없거나 이미 활성 상태" }
    }
    else { throw "restore에는 -Id 또는 -All 이 필요합니다" }
  }

  'archive' {
    if (-not $Id) { throw "archive에는 -Id 가 필요합니다" }
    $now = [DateTimeOffset]::UtcNow.ToUnixTimeMilliseconds()
    sqlite3 $db "UPDATE session SET time_archived = $now WHERE id = '$Id';"
    "보관 완료: $Id"
  }

  'purge' {
    $c = sqlite3 $db "SELECT COUNT(*) FROM session WHERE time_archived > 0;"
    $ans = Read-Host "보관된 $c 개 세션을 영구 삭제할까요? (yes 입력)"
    if ($ans -eq 'yes') {
      sqlite3 $db "DELETE FROM session WHERE time_archived > 0;"
      "$c 개 삭제 완료"
    } else { "취소" }
  }
}
```

**사용법**

```powershell
# 목록
.\session-archive.ps1
.\session-archive.ps1 -Action list -Like STM32

# 단일 복구
.\session-archive.ps1 -Action restore -Id ses_f1dedf62bffeUn3w13LhYeiLsS

# 전체 복구
.\session-archive.ps1 -Action restore -All

# 재보관
.\session-archive.ps1 -Action archive -Id ses_xxx
```

> ⚠️ `purge`(영구 삭제)는 **되돌릴 수 없습니다.** 스크립트는 `yes`를 정확히 입력해야만 실행됩니다.

### 10.2 안전한 배치 스크립트 (백업 자동)

```powershell
# 복사해 쓰세요 — 백업 후 전체 복구
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
$bk = "$env:USERPROFILE\Desktop\opencode-before-restore-$(Get-Date -Format 'yyyyMMdd-HHmmss').db"

Write-Host "① 백업 중..." -ForegroundColor Cyan
Copy-Item -LiteralPath $db -Destination $bk
Write-Host "   → $bk" -ForegroundColor Green

$n = sqlite3 $db "SELECT COUNT(*) FROM session WHERE time_archived > 0;"
if ($n -eq '0') { Write-Host "보관된 세션 없음"; return }

Write-Host "② $n 개 세션 복구 중..." -ForegroundColor Cyan
sqlite3 $db "UPDATE session SET time_archived = NULL WHERE time_archived > 0;"

Write-Host "③ 검증" -ForegroundColor Cyan
sqlite3 $db "PRAGMA integrity_check;"
sqlite3 $db "SELECT COUNT(*) AS remaining FROM session WHERE time_archived > 0;"

Write-Host "완료. opencode를 재시작하세요." -ForegroundColor Green
```

---

## 11. 키바인드 레퍼런스

### 11.1 세션 관련 (기본값, v1.18.26)

| 키바인드 키 | 기본 단축키 | 동작 |
|---|---|---|
| `session_list` | `<leader>l` = `ctrl+x l` | **세션 목록 열기** ← 보관 복구 진입점 |
| `session_new` | `<leader>n` = `ctrl+x n` | 새 세션 |
| `session_rename` | `ctrl+r` | 세션 이름 바꾸기 |
| `session_delete` | `ctrl+d` | **세션 삭제** (되돌릴 수 없음 — 주의) |
| `session_export` | `<leader>x` = `ctrl+x x` | Markdown으로 내보내기 |
| `session_timeline` | `<leader>g` = `ctrl+x g` | 세션 타임라인 |
| `session_fork` | `none` | 세션 분기 |
| `session_share` | `none` | 공유 |
| `session_unshare` | `none` | 공유 해제 |
| `command_list` | `ctrl+p` | 명령 팔레트 |

### 11.2 ⚠️ 아카이브 단축키는 공식 스키마에 없습니다

`https://opencode.ai/tui.json` 스키마의 `keybinds` 항목에 **`session_archive` / `session_unarchive` 키가 정의되어 있지 않습니다.**

커뮤니티 PR(#13961, #43919)에서는 다음이 보고되었습니다:

| 조작 | 출처 | 상태 |
|---|---|---|
| 세션 목록에서 Archived 항목 선택 → 자동 unarchive | PR #13961 | 동작 보고됨 |
| `tab` 으로 활성/보관 전환 | PR #13961 | 동작 보고됨 |
| 목록 내 `ctrl+a` 아카이브 토글 | PR #43919 | 동작 보고됨 |

> **주의:** 이 키들은 공식 스키마에 없어 **버전에 따라 동작하지 않을 수 있습니다.** `ctrl+a`는 프롬프트 입력창에서 `input_line_home`(줄 처음으로)이므로, 입력창에서 누르면 줄이 아니라 커서만 움직입니다.
>
> **확인법:** `/sessions` 목록 하단의 상태 표시줄에 `archive` / `unarchive` 라벨이 어떤 키를 안내하는지 표시됩니다. **그 라벨을 신뢰하세요.**

### 11.3 leader 키 조정

`tui.json`에서 리드어 키와 대기시간을 바꿀 수 있습니다.

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "leader": "ctrl+g",
  "leader_timeout": 3000
}
```

Windows 문제로 특정 키를 끄려면 `"none"` 또는 `false`:

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "keybinds": {
    "session_delete": "none"
  }
}
```

> **권장:** `session_delete`(보관 메뉴에서 삭제와 인접해 실수하기 쉬움)를 `"none"`으로 비활성화해 두면 데이터 손실 위험이 줄어듭니다.

### 11.4 tui.json 위치

이 PC에는 `tui.json` 이 **없습니다** (기본값 사용 중).

| 범위 | 경로 |
|---|---|
| 전역 (사용자) | `~/.config/opencode/tui.json` |
| Windows 실제 경로 | `C:\Users\Administrator\.config\opencode\tui.json` |

생성 후 재시작해야 적용됩니다.

---

## 12. 트러블슈팅

### 증상별 점검표

| 증상 | 원인 | 해결 |
|---|---|---|
| SQL 실행했는데 목록에 안 뜸 | opencode가 실행 중 | **완전 종료 후 재시작** |
| `/sessions`에 Archived 그룹이 안 보임 | 버전이 아카이브 UI 미지원 | §6 SQL 또는 §7 export/import |
| `ctrl+a`를 눌러도 반응 없음 | 입력창에서 `input_line_home`로 소비됨 | 목록 하단 라벨이 안내하는 키 사용 |
| `ctrl+x l`이 안 먹힘 | leader 대기시간(2000ms) 초과 | 두 키를 빠르게 연속 입력, 또는 `tui.json`에서 `leader_timeout` 상향 |
| `unable to open database file` | opencode가 DB를 잠금 | opencode 종료 후 재실행 |
| DB가 손상됨 | 실행 중에 파일 복사/수정 | 백업본으로 복원, `PRAGMA integrity_check` |
| 복구했는데 세션 내용이 이전과 다르게 보임 | export/import 경로 사용 | 세션 ID가 바뀌며 AI가 맥락을 재요약함 (§7) |
| 한글이 깨져서 조회 안 됨 | 인코딩 | `chcp 65001` 후 재시도, 또는 CLI에 `-encoding UTF8` |
| `permission` 관련 이상 | 복구 시 `permission`까지 수정 | `time_archived` 만 NULL로 되돌릴 것 (§3 함정 3) |

### 인코딩 문제 해결 (PowerShell 5.1)

```powershell
chcp 65001
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$OutputEncoding = [System.Text.Encoding]::UTF8
```

### 복구 전 필수 안전 점검

```powershell
# ① 백업
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
Copy-Item $db "$env:USERPROFILE\Desktop\opencode-backup.db"

# ② 현재 무결성
sqlite3 $db "PRAGMA integrity_check;"

# ③ 덤프 (추가 안전망)
sqlite3 $db ".dump" > "$env:USERPROFILE\Desktop\opencode-dump.sql"
```

### 백업으로 롤백

```powershell
Copy-Item "$env:USERPROFILE\Desktop\opencode-backup.db" `
          "$env:USERPROFILE\.local\share\opencode\opencode.db" -Force
# opencode 재시작
```

> **주의:** 백업본을 되돌리면 백업 이후 진행한 **새로운 세션이 모두 사라집니다.** 그래서 먼저 종료 → 백업 → 수정 → 재시작 순서를 지키세요.

---

## 13. 이 PC 현재 현황 (2026-09-27 실측)

### 13.1 환경

| 항목 | 값 |
|---|---|
| opencode 버전 | `1.18.26` |
| OS | Windows 11 / PowerShell 5.1 |
| DB 경로 | `C:\Users\Administrator\.local\share\opencode\opencode.db` |
| DB 크기 | **646.14 MB** |
| WAL 파일 | 60.12 MB |
| SQLite 버전 | 3.45.3 |
| `tui.json` | 없음 (기본 키바인드) |

### 13.2 세션 통계

| 상태 | 개수 | 비율 |
|---|---|---|
| active | 249 | 85.3% |
| **archived** | **43** | **14.7%** |
| 합계 | 292 | 100% |

### 13.3 ⚠️ 보관 패턴 이상 징후

**43개 세션이 단 4분 15초(18:11:54 ~ 18:15:49) 안에 3~4초 간격으로 연속 보관되었습니다.**

```
2026-09-27 18:11:54  양자 컴퓨터 시뮬레이터 만들기
2026-09-27 18:11:58  경력직 구인 트렌드 분석 (반도체/IT)
2026-09-27 18:12:01  반도체 트랜지스터 구조 발전사 정리
   ... 3~4초 간격 ...
2026-09-27 18:15:49  텍스트 파일 마크다운 변환 및 분류 정리
```

| 관찰 | 해석 |
|---|---|
| 보관 시각이 3~4초씩 균일 | 손으로 43번 누른 패턴이 아님 |
| 작업 디렉터리가 **전부 `C:/`** | 프로젝트가 아닌 루트 단위에서 실행됨 |
| 세션 제목이 광범위하게 분산 | 하나의 작업이 아니라 기존 세션 전체 |
| 보관 직후 세션들이 계속 갱신됨 | 일부 세션은 보관 후에도 수정됨 |

**가능성**

1. 스크립트 / 배치로 일괄 보관
2. 세션 목록에서 범위 선택 후 일괄 보관
3. 버그 또는 의도치 않은 동작

**확인 권장**

```powershell
# 43개가 정말 보관되어 있고, 활성 세션에는 영향이 없는지
$db = "$env:USERPROFILE\.local\share\opencode\opencode.db"
sqlite3 -header -column $db "SELECT CASE WHEN time_archived>0 THEN 'ARCHIVED' ELSE 'active' END AS st, COUNT(*) FROM session GROUP BY st;"
sqlite3 $db "PRAGMA integrity_check;"
```

전부 복구가 필요하면:

```powershell
.\session-archive.ps1 -Action restore -All
# 또는
sqlite3 $db "UPDATE session SET time_archived = NULL WHERE time_archived > 0;"
```

### 13.4 보관된 세션 목록 (최신순)

| # | 보관시각 | 제목 |
|---|---|---|
| 1 | 18:15:49 | 텍스트 파일 마크다운 변환 및 분류 정리 |
| 2 | 18:15:46 | TurtleBot3 Unity 맵 Quad 설정 문의 |
| 3 | 18:15:43 | Isaac Sim 최신 문서 스크래핑 및 다운로드 |
| 4 | 18:15:40 | 제약 AI 실험 오케스트레이션 연구기관 사례 |
| 5 | 18:15:34 | 유튜브 세미나 내용 정리 |
| 6 | 18:15:27 | popular_models_downloader.py 오픈소스 모델 확장 |
| 7 | 18:15:23 | Verilog/VHDL media 폴더 파일 동일 여부 확인 |
| 8 | 18:15:20 | TTL 게이트 발전 역사: BJT부터 MOS까지 |
| 9 | 18:15:16 | MakeROBO 로봇 교육 교재 구하기 |
| 10 | 18:15:13 | VHDL·Verilog PDF 챕터별 마크다운 파일 생성 |
| 11 | 18:15:10 | VGA 문서 형식 정리 |
| 12 | 18:15:07 | 마크다운 그림 가운데 정렬 |
| 13 | 18:15:03 | 제품 사용하기 챕터별 마크다운 파일 분리 |
| 14 | 18:14:59 | Altera FPGA 트렌드 및 기술 정리 |
| 15 | 18:14:55 | STM32 저전력 모드 깨우기 번역 및 마크다운 변환 |
| 16 | 18:14:51 | BSDL/IBIS 파일 사용법 마크다운 문서 작성 |
| 17 | 18:14:46 | Edge AI 양자화와 가지치기 비교 예제 |
| 18 | 18:14:41 | EasyEDA PCB 아트워크 치수 0.01 조절 |
| 19 | 18:14:35 | Isaac Sim 활용 요약 확인 |
| 20 | 18:14:28 | EasyEDA cap 지정번호 일괄 파트명 수정 방법 |
| 21 | 18:14:20 | Swift 및 KMP 교육 커리큘럼과 실습 |
| 22 | 18:14:15 | NPU AI 반도체 설계 커리큘럼 검토 |
| 23 | 18:14:10 | NPU AI반도체 설계 강의일정 포맷 변환 |
| 24 | 18:14:05 | Allama 모델 저장 위치 |
| 25 | 18:14:00 | Git 동기화 실패 해결 (빈 repo 제외) |
| 26 | 18:13:55 | 앤트로픽 AI 오용 보고서 마크다운 정리 |
| 27 | 18:13:51 | 무료 LLM 모델 로컬 설치 방법 |
| 28 | 18:13:47 | 공각 기동대 로봇 손으로 키보드 입력 분석 |
| 29 | 18:13:42 | ROBOTIS LDS-02/03 파트명 및 대치 부품 문의 |
| 30 | 18:13:37 | 훌라후프 코일 차량절도 원리 분석 및 마크다운 정리 |
| 31 | 18:13:32 | NASA Power of 10 규칙 교육 자료 작성 |
| 32 | 18:13:28 | JSF C++ 규격 존재 여부 |
| 33 | 18:13:23 | Unity TurtleBot3 IMU 0값 이유 |
| 34 | 18:13:17 | 무료 거버 뷰어로 아웃라인 DXF 추출 |
| 35 | 18:13:12 | 인벤터에서 FreeCAD로 3D 설계 이전 및 수정 방법 |
| 36 | 18:13:05 | _hub/router 기반 네트워크 속도 측정 |
| 37 | 18:12:59 | 로컬 LLM 환경 분석 및 최적 모델 설치 가이드 |
| 38 | 18:12:54 | 로봇 채용 동향 분석 및 교육 제안 |
| 39 | 18:12:20 | 로봇 학습 방법 및 최신 동향 조사 |
| 40 | 18:12:13 | 우분투 PC XDMCP 원격 접속 문의 |
| 41 | 18:12:08 | 반도체 트랜지스터 구조 발전사 정리 |
| 42 | 18:12:01 | 경력직 구인 트렌드 분석 (반도체/IT) |
| 43 | 18:11:54 | 양자 컴퓨터 시뮬레이터 만들기 |

> ID로 복구하려면:
> ```powershell
> sqlite3 -header -column "$env:USERPROFILE\.local\share\opencode\opencode.db" "SELECT id, title FROM session WHERE time_archived>0 ORDER BY time_archived DESC;"
> ```

---

## 14. 참고 링크

### 공식 문서

| 용도 | URL |
|---|---|
| TUI | <https://opencode.ai/docs/tui/> |
| CLI | <https://opencode.ai/docs/cli/> |
| 키바인드 | <https://opencode.ai/docs/keybinds/> |
| **tui.json 스키마 (권위 있는 정본)** | <https://opencode.ai/tui.json> |
| **opencode.json 스키마** | <https://opencode.ai/config.json> |
| Windows / WSL 가이드 | <https://opencode.ai/docs/windows-wsl> |

### GitHub 이슈 · PR

| 용도 | 링크 |
|---|---|
| TUI 아카이브/언아카이브 추가 | PR #13961 |
| unarchive 동작 수정 + 팔레트 검색 | PR #43919 |
| Settings > Archived Sessions UI | PR #15250 |
| "보관한 세션을 어디서 찾나" (원 이슈) | Issue #12888 |
| "아카이브 취소 방법" | Issue #12393 |
| TUI 빠른 unarchive 액션 요청 | Issue #28053 |
| 역방향성 아카이브 개선 요청 | Issue #31104 |

### 관련 도구

| 용도 | 링크 |
|---|---|
| SQLite GUI (Windows용, DB 직접 열기) | <https://github.com/little-brother/sqlite-gui> |
| DB 경로 확인 | `opencode db path` |
| 세션 내보내기 | `opencode export <sessionID>` |
| 세션 가져오기 | `opencode import <file>` |

---

## 부록. 전체 시나리오 시나리오 매뉴얼

### 시나리오 A — 세션 하나를 실수로 보관했을 때

```
1. /sessions  (또는 ctrl+x l)
2. 목록 하단 "Archived" 그룹으로 스크롤
3. 해당 제목 검색 (팔레트 ctrl+p 도 가능)
4. Enter 로 선택  →  자동 보관 해제 + 세션 오픈
5. (안 되면) 하단 라벨의 unarchive 키 누르기
```

### 시나리오 B — 잘못 일괄 보관했을 때 (이 PC 현재 상황)

```
1. opencode 완전 종료  (/exit)
2. 백업
   Copy-Item "$env:USERPROFILE\.local\share\opencode\opencode.db" `
             "$env:USERPROFILE\Desktop\opencode-backup-$(Get-Date -Format yyyyMMdd-HHmmss).db"
3. 먼저 현재 목록 확인
   sqlite3 -header -column "$env:USERPROFILE\.local\share\opencode\opencode.db" `
     "SELECT COUNT(*) AS n FROM session WHERE time_archived>0;"
4. 복구
   sqlite3 "$env:USERPROFILE\.local\share\opencode\opencode.db" `
     "UPDATE session SET time_archived = NULL WHERE time_archived > 0;"
5. 검증
   sqlite3 "$env:USERPROFILE\.local\share\opencode\opencode.db" "PRAGMA integrity_check;"
   sqlite3 "$env:USERPROFILE\.local\share\opencode\opencode.db" "SELECT COUNT(*) FROM session WHERE time_archived>0;"   # 0 Expected
6. opencode 재시작
7. /sessions 로 확인
```

### 시나리오 C — 다른 PC로 세션 옮기기

```
1. 원본 PC: opencode export > C:\Users\Administrator\Desktop\s.json
2. 대상 PC: s.json 를 opencode 가동 폴더로 복사
3. 대상 PC: opencode import s.json
4. 대상 PC: opencode session list  로 새 세션 확인
※ 새 세션 ID가 부여되므로 기존 공유 링크는 더 이상 유효하지 않습니다
```

### 시나리오 D — 실수로 세션 삭제까지 했을 때

**복구 불가.** 삭제(`session_delete`, `ctrl+d`)는 DB 레코드 자체를 지웁니다.

가능한 대안:

1. **DB 백업 파일이 있다면** — §12 "백업으로 롤백" 참고
2. **공유했다면** — 공유 URL에서 대화를 볼 수 있고, `opencode import <공유URL>` 로 재import 가능
3. **opencode의 revert 스냅샷** — `revert` 컬럼에 파일 변경 이력이 남아 있을 수 있으나 대화 내용 복원은 아님
4. **파일 변경 내용** — 파일은 디스크에 그대로 있으므로 파일만 회수 가능

> **예방:** `tui.json` 에서 `session_delete` 를 `"none"` 으로 비활성화하세요.
> ```json
> { "$schema": "https://opencode.ai/tui.json", "keybinds": { "session_delete": "none" } }
> ```

---

*문서 끝 · 작성 2026-09-27 · opencode 1.18.26 / Windows 11 실측 검증*
*opencode는 빠르게 갱신됩니다. UI 동작이나 키바인드가 바뀌면 <https://opencode.ai/docs/tui/> 및 <https://opencode.ai/tui.json> 에서 최신 정본을 확인하세요.*
