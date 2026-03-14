# 부록

---

## 부록 A: Claude Code 설치 빠른 참조 가이드

### 운영체제별 설치 명령어

| OS | 명령어 |
|----|--------|
| macOS | `npm install -g @anthropic-ai/claude-code` |
| Ubuntu/Debian | `npm install -g @anthropic-ai/claude-code` |
| Windows WSL2 | WSL2 Ubuntu 내에서 위 명령어 실행 |

### 필수 사전 요구사항

```bash
# Node.js 18+ 확인
node --version

# npm 8+ 확인
npm --version

# Claude Code 설치 후 확인
claude --version
claude doctor
```

### 자주 발생하는 설치 오류

| 오류 | 원인 | 해결 방법 |
|------|------|----------|
| `EACCES permission denied` | npm 권한 문제 | `npm config set prefix '~/.npm-global'` 설정 |
| `claude: command not found` | PATH 미설정 | `~/.npm-global/bin`을 PATH에 추가 |
| `Cannot find module` | Node.js 버전 낮음 | Node.js 18+ 재설치 |
| `ANTHROPIC_API_KEY not set` | 환경변수 미설정 | `.bashrc`/`.zshrc`에 key 추가 |

### Anthropic API 키 발급

1. `console.anthropic.com` 접속
2. 계정 생성 또는 로그인
3. API Keys → Create Key
4. 키를 복사하여 안전하게 보관

---

## 부록 B: 핵심 슬래시 명령어 레퍼런스

| 명령어 | 설명 | 사용 예 |
|--------|------|---------|
| `/help` | 사용 가능한 명령어 목록 표시 | `/help` |
| `/exit` | 세션 종료 | `/exit` |
| `/quit` | 세션 종료 (별칭) | `/quit` |
| `/clear` | 대화 기록 초기화 | `/clear` |
| `/compact` | 컨텍스트 압축 | `/compact` |
| `/model` | 사용 모델 변경 | `/model claude-opus-4-5` |
| `/cost` | 현재 세션 비용 확인 | `/cost` |
| `/doctor` | 환경 진단 | `/doctor` |
| `/status` | 현재 상태 확인 | `/status` |
| `/version` | 버전 확인 | `/version` |
| `/review` | 코드 리뷰 요청 | `/review src/auth.ts` |
| `/vim` | Vim 모드 전환 | `/vim` |
| `/config` | 설정 보기/변경 | `/config` |

---

## 부록 C: settings.json 완전 레퍼런스

### 설정 파일 위치

| 범위 | 위치 |
|------|------|
| 전역 | `~/.claude/settings.json` |
| 프로젝트 | `./.claude/settings.json` |

### 전체 설정 옵션

```json
{
  // 기본 모델 설정
  "model": "claude-sonnet-4-6",

  // 권한 설정
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm *)",
      "Read(*)",
      "Write(src/**)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(sudo *)"
    ]
  },

  // MCP 서버 설정
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  },

  // 훅 설정
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.claude/hooks/pre-bash.py"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write $CLAUDE_TOOL_INPUT_FILE_PATH"
          }
        ]
      }
    ]
  },

  // 환경 변수 (훅에서 사용)
  "env": {
    "MY_VAR": "my_value"
  }
}
```

---

## 부록 D: CLAUDE.md 템플릿 모음

### 템플릿 1: Python 백엔드 (FastAPI)

```markdown
# 프로젝트명

## 기술 스택
- Python 3.11+
- FastAPI 0.100+
- PostgreSQL + SQLAlchemy + Alembic
- Redis (캐싱)
- pytest (테스트)

## 코딩 규칙
- 타입 힌트 필수
- async/await 사용
- Pydantic v2로 데이터 검증
- 함수당 30줄 이하

## 프로젝트 구조
```
app/
├── api/routers/    # API 라우터
├── core/           # 설정, 의존성
├── models/         # SQLAlchemy 모델
├── schemas/        # Pydantic 스키마
└── services/       # 비즈니스 로직
```

## 개발 명령어
```bash
uvicorn app.main:app --reload  # 개발 서버
pytest -v                       # 테스트
alembic upgrade head            # DB 마이그레이션
```
```

### 템플릿 2: React 프론트엔드

```markdown
# 프로젝트명 Frontend

## 기술 스택
- React 18 + TypeScript
- Vite (빌드)
- Tailwind CSS (스타일링)
- React Query (서버 상태)
- Zustand (클라이언트 상태)
- Vitest (테스트)

## 컴포넌트 규칙
- 함수형 컴포넌트만 사용
- Props 타입 반드시 정의
- 컴포넌트 파일: PascalCase.tsx
- 훅 파일: use{Name}.ts

## 금지 사항
- any 타입 사용 금지
- index.tsx 이외 파일에서 default export 금지
- 인라인 스타일 금지 (Tailwind 사용)
```

### 템플릿 3: Go 마이크로서비스

```markdown
# 서비스명

## 기술 스택
- Go 1.21+
- Gin (HTTP 프레임워크)
- GORM (ORM)
- gRPC (내부 통신)
- testify (테스트)

## 패키지 구조
```
cmd/           # 진입점
internal/
  handler/     # HTTP/gRPC 핸들러
  service/     # 비즈니스 로직
  repository/  # 데이터 접근
  domain/      # 도메인 모델
pkg/           # 공유 유틸리티
```

## 규칙
- 에러는 wrap해서 컨텍스트 추가
- 인터페이스로 의존성 주입
- 테이블 드리븐 테스트 선호
```

---

## 부록 E: 추천 MCP 서버 디렉토리

| MCP 서버 | npm 패키지 | 주요 기능 |
|---------|------------|---------|
| GitHub | `@anthropic-ai/mcp-server-github` | 이슈, PR, 코드 |
| PostgreSQL | `@anthropic-ai/mcp-server-postgres` | DB 쿼리, 스키마 |
| Slack | `@anthropic-ai/mcp-server-slack` | 메시지 읽기/쓰기 |
| Filesystem | `@anthropic-ai/mcp-server-filesystem` | 파일 시스템 확장 |
| Fetch | `@anthropic-ai/mcp-server-fetch` | 웹 페이지 가져오기 |
| SQLite | `mcp-server-sqlite` | SQLite 데이터베이스 |
| Brave Search | `mcp-server-brave-search` | 웹 검색 |

---

## 부록 F: 자주 묻는 질문(FAQ) 및 문제 해결

**Q1. Claude Code와 GitHub Copilot을 함께 사용해도 되나요?**
A: 네, 함께 사용하면 더 좋습니다. IDE에서 빠른 자동완성은 Copilot, 복잡한 작업 자동화는 Claude Code를 사용하세요.

**Q2. 컨텍스트 한계에 도달하면 어떻게 하나요?**
A: `/compact` 명령으로 대화를 압축하거나, `/clear`로 새로 시작하세요. 중요한 정보는 CLAUDE.md에 기록해두면 새 세션에서도 유지됩니다.

**Q3. API 비용이 너무 많이 나올 것 같아요**
A: Claude Sonnet 모델은 Opus 대비 5배 저렴합니다. 일반 작업엔 Sonnet을, 복잡한 분석에만 Opus를 사용하세요. `.claudeignore`로 불필요한 파일도 제외하세요.

**Q4. 팀 전체가 하나의 API 키를 사용해도 되나요?**
A: 기술적으로는 가능하지만, 개인별 키 사용을 권장합니다. 비용 추적과 보안 관리가 용이합니다.

**Q5. Claude Code가 잘못된 코드를 생성했어요. 어떻게 하나요?**
A: "이 코드에 버그가 있어. [버그 설명]. 수정해줘"라고 피드백하세요. Claude Code는 피드백을 바탕으로 수정합니다.

**Q6. 민감한 코드(결제, 인증)도 Claude Code에게 보여줘도 되나요?**
A: Anthropic은 API로 전송된 코드를 모델 훈련에 사용하지 않습니다(Enterprise 계약의 경우 보장됨). 그러나 실제 시크릿값(API 키, 비밀번호)은 전송하지 마세요.

**Q7. 인터넷 연결 없이 Claude Code를 사용할 수 있나요?**
A: 아니요. Claude Code는 Anthropic API 서버에 연결이 필요합니다. 오프라인 환경에서는 사용할 수 없습니다.

**Q8. Claude Code가 실수로 중요한 파일을 삭제했어요**
A: `git`을 사용하고 있다면 `git checkout -- <파일>` 또는 `git stash`로 복원하세요. Claude Code를 사용하기 전에 항상 중요한 변경사항을 커밋해두는 습관을 들이세요.

**Q9. Windows에서 Claude Code를 사용할 수 있나요?**
A: WSL2를 통해 사용 가능합니다. Native Windows(PowerShell, CMD)는 지원되지 않습니다.

**Q10. Claude Code의 응답이 너무 느려요**
A: Claude Sonnet 또는 Haiku 모델로 변경해보세요. 또는 컨텍스트 크기를 줄이면 응답이 빨라집니다.
