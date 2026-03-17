> 한국어 | **[English](README.md)**

# API Radar

GitHub 저장소의 API 엔드포인트를 **읽기 전용**으로 분석하여 구조화된 **Endpoint Reference Card**로 문서화하는 에이전트 스킬입니다.

경로 검색, 키워드 검색, 기능 설명 기반 검색, PR/커밋/브랜치 분석을 지원합니다.

## 왜 API Radar인가?

- **즉석 API 문서** — GitHub 저장소를 지정하면 수 초 내에 구조화된 엔드포인트 문서 생성
- **PR 리뷰 도우미** — 승인 전 PR이 도입하는 API 변경사항을 빠르게 파악
- **온보딩 가속기** — 처음 접하는 코드베이스에서 원하는 API를 빠르게 찾아 분석
- **설정 불필요** — 7개 프레임워크를 기본 지원, 별도 설정 없이 바로 사용

## 주요 기능

- **경로 검색** — 정확한 경로로 엔드포인트 탐색 (예: `/v1/users/{user_id}`)
- **키워드 검색** — 식별자, ViewSet 이름, 라우트 일부로 검색
- **기능 설명 검색** — 자연어로 기능을 설명하면 관련 엔드포인트를 찾아줌
- **PR / 커밋 / 브랜치 분석** — 특정 PR, 커밋, 브랜치에서 도입된 API 변경사항 분석
- **멀티 프레임워크 지원** — Django, DRF, FastAPI, Django Ninja, Express, NestJS, Spring
- **구조화된 출력** — 인증, 요청, 응답, 에러, 근거, 검증용 curl snippet을 포함한 Endpoint Reference Card 형식으로 출력
- **읽기 전용** — 코드, 파일, 브랜치, PR을 절대 생성/수정/삭제하지 않음

## 설치

[npx skills](https://github.com/vercel-labs/skills)를 통해 설치합니다:

```bash
npx skills add divlook/agent-skill-api-radar
```

> 스킬은 **`api-radar`** 라는 이름으로 설치됩니다 (`agent-skill-api-radar`가 아님).
> 설치 후 에이전트는 `api-radar`로 스킬을 인식합니다.

### 특정 에이전트에만 설치

```bash
# OpenCode만
npx skills add divlook/agent-skill-api-radar -a opencode

# Claude Code만
npx skills add divlook/agent-skill-api-radar -a claude-code

# 여러 에이전트
npx skills add divlook/agent-skill-api-radar -a opencode -a claude-code -a cursor
```

### 전역 설치 (모든 프로젝트에서 사용 가능)

```bash
npx skills add divlook/agent-skill-api-radar -g
```

### 수동 설치

`skills/api-radar/`를 에이전트의 skills 디렉토리에 복사합니다:

| 에이전트 | 프로젝트 경로 | 전역 경로 |
|---------|-------------|---------|
| OpenCode | `.agents/skills/api-radar/` | `~/.config/opencode/skills/api-radar/` |
| Claude Code | `.claude/skills/api-radar/` | `~/.claude/skills/api-radar/` |
| Cursor | `.agents/skills/api-radar/` | `~/.cursor/skills/api-radar/` |

## 필요 사항

- [GitHub CLI (`gh`)](https://cli.github.com/) — PR/커밋/브랜치 분석 시 필요
- GitHub CLI 인증 (`gh auth login`) — 비공개 저장소 및 PR/커밋/브랜치 분석 시 필요

경로/키워드/기능 설명 검색은 에이전트 내장 파일 탐색 도구를 사용하므로 `gh`가 필요하지 않습니다.

## 사용법

### 빠른 시작

```
/api-radar example-owner/example-repo /v1/users
```

**저장소**와 **쿼리**를 입력하면 Endpoint Reference Card가 반환됩니다.

### 쿼리 유형

| 유형 | 예시 | 언제 사용 |
|------|------|---------|
| API 경로 | `example-owner/example-repo /companies/{company_id}/users` | 정확한 경로를 알 때 |
| 키워드 | `example-owner/example-repo partner` | 엔드포인트 이름 일부를 알 때 |
| 기능 설명 | `example-owner/example-repo 급여 이체 API` | 기능만 알 때 |
| 저장소 URL | `https://github.com/example-owner/example-repo /v1/health` | URL로 저장소 지정 시 |
| PR 번호 | `example-owner/example-repo PR#123` | PR의 API 변경사항 분석 시 |
| 커밋 | `example-owner/example-repo commit 1a2b3c4` | 특정 커밋 분석 시 |
| 브랜치 | `example-owner/example-repo branch feature/payments` | 특정 브랜치 분석 시 |
| 별칭 | `my-api /v1/health` | 설정된 별칭 사용 시 |

### 출력 예시

```
## Endpoint Reference Card

### POST /companies/{company_id}/salary

**Purpose**: 주어진 회사의 전체 직원에 대한 급여 이체를 시작합니다.

**Auth**: Bearer JWT — SALARY_TRANSFER 권한 코드 필요 (AccessType.PERMISSION)

**Request**
- Path params: company_id (integer, required)
- Headers: Authorization: Bearer {TOKEN}
- Body: { "transfer_date": "YYYY-MM-DD", "memo": "string" }

**Response (Observed)**
- Status: 200
- Body: { "transfer_id": 1234, "status": "pending" }

**Errors (Observed)**
- 403 PERMISSION_DENIED — SALARY_TRANSFER 권한 없음
- 400 INVALID_DATE — transfer_date가 과거 날짜

#### Evidence
- Routing: https://github.com/owner/repo/blob/abc1234/api/salary/urls.py#L12-L15
- Permission: https://github.com/owner/repo/blob/abc1234/api/salary/views.py#L45
```

## 커스터마이즈

### 저장소 별칭

설치된 스킬의 `references/repo-aliases.md` 파일을 편집하여 자주 사용하는 저장소에 짧은 별칭을 등록할 수 있습니다.

```markdown
| alias     | owner/repo               | note                    |
|-----------|--------------------------|-------------------------|
| my-api    | your-org/your-api        | Django REST Framework   |
| payments  | your-org/payment-service | FastAPI                 |
```

등록 후 쿼리에서 별칭을 바로 사용할 수 있습니다:

```
/api-radar my-api /v1/users
/api-radar payments PR#42
```

파일 위치:

| 에이전트 | 프로젝트 경로 |
|---------|-------------|
| Claude Code | `.claude/skills/api-radar/references/repo-aliases.md` |
| OpenCode, Cursor | `.agents/skills/api-radar/references/repo-aliases.md` |

## 지원 프레임워크

| 언어 | 프레임워크 |
|------|---------|
| Python | Django, Django REST Framework (DRF), FastAPI, Django Ninja |
| Node.js | Express, NestJS |
| Java | Spring (RestController) |

프레임워크 탐지는 best-effort로 동작합니다. 확정하지 못해도 동일한 출력 포맷으로 분석을 진행하며, 불확실한 항목은 `Uncertainties` 섹션에 명시됩니다.

## 라이선스

MIT — [LICENSE](LICENSE) 참조
