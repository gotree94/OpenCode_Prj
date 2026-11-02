# OpenCode 컨텍스트/사용량 관리 정리

작성일: 2026-11-03

## 1. 요약

OpenCode에는 컨텍스트 사용량을 확인하고 줄이는 기능이 이미 내장되어 있습니다.
다만 OpenCode가 알 수 있는 것은 **토큰 사용량/비용 통계**이며, provider의
**남은 API 쿼터**까지 알 수는 없습니다.

정리하면 다음과 같습니다.

- 남은 컨텍스트: TUI 표시 또는 `/compact`로 관리
- 사용한 토큰/비용: `opencode stats`
- 남은 API 쿼터: provider 대시보드에서 확인
- 자동 관리: `compaction.auto`, `prune`, `reserved` 설정

---

## 2. 사용량 확인

CLI에서 다음 명령을 실행합니다.

```powershell
opencode stats
```

확인 당시 전체 누적 통계 예시:

```text
Sessions      491
Messages      8,977
Input         34.8M
Output        6.6M
Cache Read    550.4M
Total Cost    $0.00
```

이 값은 **전체 누적 사용량**입니다. 현재 세션의 남은 토큰을 정확히 보여주는
값은 아닙니다.

프로젝트 기준으로 보고 싶다면:

```powershell
opencode stats --project ""
opencode stats --days 1
opencode stats --models
```

---

## 3. 컨텍스트 줄이기

OpenCode TUI에서 사용하는 명령:

```text
/compact     현재 세션을 요약해서 컨텍스트 압축
/summarize   /compact와 동일
/new         새 세션 시작
/sessions    세션 목록/전환
```

단축키:

```text
Ctrl+X C     compact
Ctrl+X N     new session
Ctrl+X L     sessions
```

긴 대화가 부담되면 `/compact`로 요약하거나 `/new`로 새 세션을 여는 것이 가장
확실합니다.

---

## 4. 자동 compaction 설정

`opencode.json`에서 조절할 수 있습니다.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "compaction": {
    "auto": true,
    "prune": true,
    "reserved": 10000
  }
}
```

- `auto: true` → 컨텍스트가 차면 자동 요약
- `prune: true` → 오래된 tool 출력 제거로 토큰 절약
- `reserved: 10000` → compaction 중 overflow 방지용 여유 버퍼

`auto`는 기본값이 `true`입니다. 자동 압축을 끄고 수동으로 `/compact`만 쓰려면
`false`로 두면 됩니다.

> `prune`은 오래된 tool 결과를 제거하므로, 이전 실행 결과를 다시 참고해야 하는
> 작업에서는 주의해서 사용해야 합니다.

---

## 5. 모델 context limit 설정

커스텀 provider를 쓰는 경우 `limit.context`가 없으면 OpenCode가 남은
컨텍스트를 제대로 계산하지 못할 수 있습니다.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "models": {
        "my-model": {
          "limit": {
            "context": 200000,
            "output": 32000
          }
        }
      }
    }
  }
}
```

- `limit.context`: 모델이 받는 최대 입력 토큰
- `limit.output`: 모델이 생성할 최대 출력 토큰

이 값이 맞아야 TUI에서 컨텍스트 사용률 계산과 자동 compaction이 정상
동작합니다.

---

## 6. 직접 구현한다면

OpenCode 밖에서 직접 컨텍스트를 관리하려면 다음이 필요합니다.

1. API 응답의 `usage` 값 기록
   - `prompt_tokens`
   - `completion_tokens`
   - `cached_tokens`
2. 요청 전 토큰 수 계산
3. 70~80% 도달 시 이전 대화 요약
4. tool 출력/파일 전체 붙여넣기 제거
5. `max_output_tokens` 제한
6. 오래된 세션은 별도 저장 후 새 세션 시작

하지만 OpenCode를 쓰는 환경이라면 `/compact`, `compaction` 설정,
`opencode stats` 조합으로 대부분 해결됩니다.

---

## 7. 한계

- OpenCode는 provider의 남은 API 쿼터를 알 수 없습니다.
- `limit.context`가 잘못 설정되면 컨텍스트 사용률이 부정확해질 수 있습니다.
- `opencode stats`는 누적 통계이며, 현재 세션의 실시간 잔여량을 보여주지
  않습니다.
- 정확한 쿼터/결제 사용량은 OpenCode 또는 모델 provider 대시보드에서 확인해야
  합니다.
