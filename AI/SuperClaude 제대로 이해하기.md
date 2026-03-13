# SuperClaude 제대로 이해하기
## 들어가기 전에: Claude Code가 뭔가요?

SuperClaude를 이해하려면 먼저 Claude Code부터 알아야 해요.

Claude Code는 터미널에서 쓰는 AI 코딩 도구예요. 그냥 채팅하는 Claude와 다르게, **실제로 파일을 열고, 코드를 수정하고, 터미널 명령까지 직접 실행**해요. 말만 하는 게 아니라 행동하는 거죠.

```
일반 Claude 채팅 → "이렇게 코드를 짜면 돼요" (말만 함)
Claude Code → 파일 열고 → 직접 수정하고 → 테스트까지 실행 (행동함)
```

이렇게 스스로 판단하고 행동하는 AI를 **에이전트(Agent)** 라고 불러요.

---

## SuperClaude의 본질

한 줄로 말하면:

> **매번 길게 입력해야 하는 프롬프트를 짧은 커맨드 하나로 대신해주는 도구**

개발하다 보면 Claude Code에게 이런 말을 반복하게 돼요:

> "백엔드 개발자 관점에서, 보안도 신경 쓰면서, 테스트 코드도 함께 작성해줘. 그리고 먼저 설계부터 확인하고 구현해줘."

SuperClaude를 설치하면 이걸 그냥 `/sc:implement` 한 줄로 끝낼 수 있어요.

---

## 실제 파일 구조로 이해하기

SuperClaude를 설치하면 이런 파일들이 생겨요:

```
~/.claude/commands/sc/
├── implement.md    ← /sc:implement 커맨드의 실체
├── brainstorm.md   ← /sc:brainstorm 커맨드의 실체
├── debug.md
├── test.md
└── ... (총 30개)
```

**이 .md 파일들이 SuperClaude의 전부예요.**

`/sc:implement "JWT 로그인 만들어줘"` 를 입력하면:

```
1. Claude Code가 implement.md 파일을 읽음
2. 그 안에 적힌 지침이 프롬프트에 자동으로 삽입됨
3. Claude가 그 지침대로 행동
```

즉 SuperClaude는 **"좋은 프롬프트를 미리 만들어서 커맨드로 포장해둔 것"** 이에요.

---

## Frontmatter가 뭔가요?

각 커맨드 파일을 열어보면 맨 위에 이런 부분이 있어요:

```
---
name: implement
personas: [architect, frontend, backend, security]
mcp-servers: [context7, sequential, magic]
---

# 본문 내용 (실제 지침)...
```

`---` 로 감싸진 위쪽 부분을 **Frontmatter** 라고 해요.

책으로 비유하면:
- **Frontmatter** = 책 표지 (제목, 장르 등 기본 정보)
- **본문** = 실제 내용

Claude Code는 Frontmatter를 읽어서 "이 커맨드는 어떤 성격인지" 파악하고, Claude(AI)는 본문을 읽어서 실제로 어떻게 행동할지 결정해요.

---

## 페르소나는 어디 있나요?

SuperClaude를 보면 `backend`, `security`, `frontend` 같은 페르소나 얘기가 나와요. 그런데 별도의 페르소나 파일이 따로 있는 게 아니에요.

**Frontmatter에 선언되고, 본문 지침 안에 녹아있어요.**

```
personas: [backend, security]
↓
본문에 "백엔드 서버 로직을 처리할 때는 이렇게 해라",
      "보안 관련 코드는 반드시 이 기준을 따라라" 같은
      지침이 텍스트로 적혀있음
↓
Claude가 그 텍스트를 읽고 알아서 해당 전문가처럼 행동
```

마법 같은 기술이 아니라, 결국 **잘 쓰여진 글(프롬프트)** 이에요.

---

## SuperClaude vs Superpowers

Claude Code 생태계에서 SuperClaude랑 자주 비교되는 **Superpowers** 라는 도구가 있어요. 둘 다 Claude Code를 강화해주지만 방향이 달라요.

| | SuperClaude | Superpowers |
|---|---|---|
| **핵심** | 커맨드 30개 제공 | 개발 방법론 강제 |
| **방식** | 슬래시 커맨드 | 스킬(자동 발동) |
| **철학** | "이 커맨드 써서 작업해" | "반드시 이 순서를 따라" |
| **Anthropic 공인** | ❌ | ✅ 공식 마켓 등록 |
| **GitHub 인기** | ⭐ 21,000 | ⭐ 28,000 |

### 커맨드 vs 스킬, 뭐가 다른가요?

```
커맨드 (SuperClaude)
→ 사용자가 /sc:implement 를 직접 입력해야 실행됨

스킬 (Superpowers)
→ Claude가 상황을 보고 "이 스킬이 필요하겠다" 판단해서 자동 발동
```

### Superpowers의 특징

Superpowers는 **개발 순서를 강제**해요. 코드를 바로 짜려 해도:

```
브레인스토밍 → 계획 작성 → TDD 구현 → 코드 리뷰 → 마무리
```

이 순서를 건너뛸 수 없어요. 선택이 아닌 의무예요.

### 어떤 걸 써야 할까요?

```
빠르게 커맨드로 작업하고 싶다  →  SuperClaude
체계적인 개발 습관을 들이고 싶다  →  Superpowers
둘 다 설치해서 함께 사용       →  충돌 없이 같이 써도 됨
```

---

## 한 줄 정리

```
SuperClaude  =  프롬프트 템플릿 30개를 커맨드로 포장한 도구
Superpowers  =  좋은 개발 습관을 자동으로 강제하는 도구
```

둘 다 결국 **"Claude Code를 더 잘 쓰기 위한 프롬프트 엔지니어링"** 이에요. 복잡해 보여도 본질은 단순해요.



