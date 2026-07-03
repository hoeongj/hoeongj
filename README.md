### 숭실대학교 컴퓨터학부 · 백엔드 / 풀스택

프로덕션 멀티서비스 시스템을 직접 **설계 · 구현 · 배포 · 운영**합니다.
대표 작업은 학교 시스템(u-SAINT · LMS · 도서관)을 표준 **MCP 서버**로 노출하고
웹 · 챗봇 · 에이전트로 소비하는 **숭실대 캠퍼스 AI 어시스턴트**입니다.

🟢 **Live** — 챗봇/대시보드 <https://ssuai.vercel.app> · MCP 서버 `https://ssumcp.duckdns.org/mcp`

---

## 숭실대 캠퍼스 AI 어시스턴트

학생이 *"오늘 학식 뭐야"*, *"이번 학기 성적 알려줘"*, *"도서관 빈자리 예약해줘"* 를
자연어로 묻고 **행동까지** 시키는 캠퍼스 어시스턴트. 공개 API 문서가 없는 교내 시스템을
브라우저 wire 캡처로 역공학해 붙이고, MCP 표준으로 노출해 웹 · Claude Desktop · ChatGPT
어디서든 쓸 수 있게 만들었다. k3s에 GitOps로 무중단 배포 · 운영 중이다.

![ssuAI 대시보드 — 학식 · 시설 · 도서관 · 공지 · u-SAINT/LMS 통합](https://raw.githubusercontent.com/hoeongj/ssuAI/main/docs/assets/dashboard.png)

```
브라우저 ── /api/*  ───────────────────────────────────┐
        └─ /api/agent/* ─ proxy ─ ssuAgent ─ MCP ───────┤
                          (LangGraph · SSE · HITL)       ▼
                              ssuMCP (Spring Boot · MCP 서버 · 52 tools)
                                     │  Connector 패턴
                                     ▼
                u-SAINT(SAP) · LMS(Canvas) · 도서관(Pyxis) · 학식 · 공지
```

### 구성 서비스

| 레포 | 역할 | 핵심 스택 |
|---|---|---|
| **[ssuMCP](https://github.com/hoeongj/ssuMCP)** | MCP 서버 — 캠퍼스 데이터·액션 도구 **52종** | Java 21 · Kotlin · Spring Boot 4 · Spring AI · PostgreSQL/pgvector · Redis/Redisson |
| **[ssuAI](https://github.com/hoeongj/ssuAI)** | 웹 클라이언트 — 5탭 반응형 UI(다크모드) + 자연어 챗봇 | Next.js 16 · TypeScript · TanStack Query · Tailwind/shadcn · Vitest |
| **[ssuAgent](https://github.com/hoeongj/ssuAgent)** | LangGraph 멀티에이전트 — SSE 스트리밍 · HITL | Python · LangGraph · FastAPI · 멀티프로바이더 LLM 폴백 |
| **[ssu-ai-service](https://github.com/hoeongj/ssu-ai-service)** | B2B 임베딩 게이트웨이 — 인증·키 위생 설계, k3s 배포(non-root) | Python · FastAPI |

### 엔지니어링 하이라이트

- **MCP 서버 (52 tools)** — 공개/인증 도구 분리, OAuth(Auth0 DCR로 ChatGPT 커넥터 지원) + classic 2-모드, **3-tier 세션 해소**로 멀티클라이언트 환경에서 안전한 인증.
- **하이브리드 RAG** — 학칙·졸업·장학 답변 근거를 lexical + 벡터 임베딩 **RRF 융합**으로 검색(pgvector ANN 프로파일 prod 라이브, Testcontainers IT로 실증). 무료 임베딩 quota를 *요청 수가 아닌 TPM*으로 실측 분석해 증분 영속으로 코퍼스를 완납.
- **HITL 예약 에이전트** — 도서관 좌석 예약을 `prepare → 사용자 승인 → confirm` 2단계로만 실행. 동기 타임아웃과 비동기 워커의 이중 상태를 **단일 진실원천 + 멱등 finalize**로 해결.
- **문서 없는 시스템 역공학** — SAP WebDynpro(u-SAINT) · Pyxis(도서관)를 wire 캡처로 역공학. 추측성 수정을 일정 시점에 끊고 검증된 Rust upstream(rusaint)을 UniFFI로 통합한 **빌드 vs 도입 판단**.
- **운영 · 품질** — k3s + ArgoCD Image Updater GitOps 무중단 배포, Helm, GitHub Actions(action SHA-pin), Testcontainers + JaCoCo 커버리지 게이트, OpenTelemetry/Grafana 관측성(RED 대시보드 · Prometheus 알림 룰), n8n 운영 자동화, gitleaks · pod-security 하드닝.
- **디버깅 기록** → [트러블슈팅 하이라이트](https://github.com/hoeongj/ssuMCP/blob/main/docs/troubleshooting-highlights.md): *틀린 가설 → 실제 원인 → 해결*로 정리한 8대 사례.

### 기술 스택

`Java 21` · `Kotlin` · `Spring Boot 4` · `Spring AI` · `Python` · `LangGraph` · `FastAPI` · `TypeScript` · `Next.js 16`
`PostgreSQL` · `pgvector` · `Redis / Redisson` · `Docker` · `k3s` · `ArgoCD` · `Helm` · `GitHub Actions` · `Vercel`
`OpenTelemetry` · `Grafana` · `Testcontainers`

---

📫 **hoengj** · akftjdwn@gmail.com
