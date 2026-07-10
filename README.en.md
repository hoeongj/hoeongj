# Hong Seong-ju · Soongsil University, School of Computer Science & Engineering · Backend / Full-stack

**한국어** [README.md](README.md) &nbsp;·&nbsp; **English** (this document) &nbsp;·&nbsp; 🌐 Portfolio site: <https://hoeongj.github.io/codegate-portfolio/>

I **design, build, deploy, and operate** production multi-service systems end to end.
My flagship work is a **campus AI assistant for Soongsil University** that exposes the school's
systems (u-SAINT · LMS · library) as a standards-compliant **MCP server**, consumed by a web app,
a chatbot, and agents.

🟢 **Try it now** → **<https://ssuai.vercel.app>** &nbsp;·&nbsp; 🗺️ Summer survival map → <https://geuneul.vercel.app>

---

## 🏆 Flagship · Soongsil Campus AI Assistant &nbsp;`production · 4 services`

**🟢 Live · 52 MCP tools · 4 services · k3s multi-pod (HPA·PDB) · zero-downtime GitOps rollout · Kafka event pipeline**

A campus assistant students can ask in natural language — *"What's for lunch today?"*,
*"Show me this semester's grades"*, *"Reserve a library seat for me"* — and have it **take action**,
not just answer. Campus systems with no public API documentation were reverse-engineered from
browser wire captures and exposed over the MCP standard, so it works from the web app, Claude
Desktop, or ChatGPT alike. Deployed and operated on k3s with GitOps.

![ssuAI dashboard — cafeteria · facilities · library · notices · u-SAINT/LMS integration](https://raw.githubusercontent.com/hoeongj/ssuAI/main/docs/assets/dashboard.png)

### Services (4 repos = one system)

| Repo | Role | Core stack |
|---|---|---|
| **[ssuMCP](https://github.com/hoeongj/ssuMCP)** | MCP server — **52 tools** for campus data & actions | Java 21 · Kotlin · Spring Boot 4 · Spring AI · PostgreSQL/pgvector · Redis/Redisson |
| **[ssuAI](https://github.com/hoeongj/ssuAI)** | Web client — 5-tab responsive UI (dark mode) + natural-language chatbot | Next.js 16 · TypeScript · TanStack Query · Tailwind/shadcn · Vitest |
| **[ssuAgent](https://github.com/hoeongj/ssuAgent)** | LangGraph multi-agent — SSE streaming · HITL | Python · LangGraph · FastAPI · multi-provider LLM fallback |
| **[ssu-ai-service](https://github.com/hoeongj/ssu-ai-service)** | B2B embedding gateway — auth & key-hygiene design, k3s deployment (non-root) | Python · FastAPI |

<details>
<summary><b>Architecture</b> (expand)</summary>

```
Browser ── /api/* ──────────────────────────────────────┐
        └─ /api/agent/* ─ proxy ─ ssuAgent ─ MCP ───────┤
                          (LangGraph · SSE · HITL)      ▼
                              ssuMCP (Spring Boot · MCP server · 52 tools)
                                     │  Connector pattern
                                     ▼
                u-SAINT (SAP) · LMS (Canvas) · Library (Pyxis) · Cafeteria · Notices
```
</details>

### Engineering Highlights

- **Safe multi-client auth** — public vs. authenticated tools are separated, with dual-mode auth (OAuth via Auth0 DCR for the ChatGPT connector, plus classic). A *request → session → client* three-tier resolution keeps auth context from bleeding as the same user moves across the web app, Claude Desktop, and ChatGPT.
- **Grounded regulation answers (hybrid RAG)** — answers about academic rules, graduation, and scholarships are returned with their source documents (lexical + vector-embedding **RRF fusion**; pgvector ANN profile live in prod, proven with Testcontainers ITs). I measured that the free embedding quota is capped by *tokens-per-minute, not request count*, and ingested the full corpus via resumable incremental persistence to stay within it.
- **HITL reservation agent** — library seat reservations run only through a two-step `prepare → user approval → confirm` flow. The dual state between the synchronous timeout and the async worker is reconciled with a **single source of truth + idempotent finalize**.
- **Reverse-engineering undocumented systems** — SAP WebDynpro (u-SAINT) and Pyxis (library) reverse-engineered from wire captures. A deliberate **build-vs-buy call**: cut off speculative patching at a fixed point and integrated the proven Rust upstream (rusaint) via UniFFI.
- **Multi-pod HA + Kafka event pipeline** — real usage is small, but I applied real production operating practices as if serving the whole student body: front/back at replicas=2 (HPA·PDB), stateful MCP sessions pinned per-pod via Traefik cookie stickiness, and tool-call / reservation notifications **split from in-memory onto a Kafka event stream** (fail-open non-blocking producer — requests pass even if the broker is down). Verified with zero-downtime rolling deploys and a **live fail-open drill** (tool calls return HTTP 200 during a broker outage).
- **Operations & quality (prod)** — zero-downtime GitOps rollout via ArgoCD Image Updater, Helm, GitHub Actions (SHA-pinned actions), Testcontainers + JaCoCo coverage gate, OpenTelemetry/Grafana observability (RED · Kafka dashboards · Prometheus alert rules), gitleaks · pod-security hardening. *Lab/experimental: Cilium eBPF FQDN egress (kind lab), n8n ops automation.*
- **Debugging log** → [Troubleshooting highlights](https://github.com/hoeongj/ssuMCP/blob/main/docs/troubleshooting-highlights.md): major cases, each written up as *wrong hypothesis → actual cause → fix*.

`Java 21` · `Kotlin` · `Spring Boot 4` · `Spring AI` · `Python` · `LangGraph` · `FastAPI` · `TypeScript` · `Next.js 16`
`PostgreSQL` · `pgvector` · `Redis / Redisson` · `Kafka` · `k3s` · `ArgoCD` · `Helm` · `GitHub Actions` · `Grafana` · `Testcontainers`

---

## 📂 Other Projects

### 🗺️ [Geuneul](https://github.com/hoeongj/geuneul) — Summer Survival Map &nbsp; 🟢 [Live](https://geuneul.vercel.app)
A living survival map that answers not just *"where"* but **"can I sit here right now, is it cool, is it crowded?"** from recent user reports. **PostGIS** indexes nationwide open data (52k public restrooms, 3.5k libraries, …) for radius (`ST_DWithin`), nearest-neighbor (kNN `<->`), and bounds queries; a `survival_score` SQL view aggregates valid reports by recency × trust to rank markers in 3 colors, plus a two-stage scenario re-ranking. The browser only ever calls a same-origin `/api/*` BFF (dodging CORS and ALB constraints at once).
`Spring Boot 4 · Java 21 · Next.js · PostGIS · Redis · AWS ECS Fargate · Terraform (IaC) · GitHub Actions OIDC · ECR/ALB/CloudFront`

### 🏠 [con-dorm](https://github.com/con-dorm) — Dormitory Roommate Matching &nbsp;`team project`
A mobile app that matches roommates by a weighted lifestyle-compatibility score and predicts dorm admission. Frontend and backend developed independently against a single **API contract**.
[Frontend (Next.js 16 · React 19)](https://github.com/con-dorm/FE) · [Backend (Java)](https://github.com/con-dorm/BE) · [Org docs](https://github.com/con-dorm)

### 🔊 [Macro](https://github.com/UNITHON24/Macro) — Voice Kiosk Ordering Assistant &nbsp;`UNITHON 2024 hackathon`
An accessibility aid that reads a kiosk screen with **EasyOCR** to index tap coordinates, then presses menu items on the user's behalf from **voice commands**. Socket-server design separating input / output / order hubs.
`Python · speech recognition (STT) · EasyOCR · TTS · screen automation`

### 🎮 DDSC — AI-graded Data-Structures Visual Novel &nbsp;`contest · team project (private)`
A childhood-friend character teaches a data structure, and **the AI grades the learner's free-text answers**, raising affection. Problems, answers, and dialogue are all human-written — **the AI only grades** → zero risk of teaching something wrong. Game integrity (affection math, beat order, endings) is owned by the engine (code), so it never breaks even if the AI misbehaves. Submitted to Soongsil's AX interactive-content contest.
`JavaScript · LLM grading (Claude/OpenAI, mock fallback)`

### 🎓 [cs-coursework](https://github.com/hoeongj/cs-coursework) — Undergraduate Coursework Portfolio &nbsp;`12 courses`
Coursework organized by subject: **Systems Programming** (C, ~2,300-line `fdupes`-style dedup tool) · **Programming Languages** (lexer → recursive-descent parser → AST tree-walking interpreter) · **Computer Architecture** (RISC-V disassembler & simulator) · **Artificial Intelligence** (Transformer · ViT · BERT from scratch) · **Algorithms** (move-semantics sorting) · **Networks** (TLS · asyncio · ZeroMQ) and more.
`C · C++ · Java · Python · PyTorch · Jupyter`

<details>
<summary>🌱 Others (early learning · collaboration utilities)</summary>

- [**RedbeanOverflow**](https://github.com/kwon32/RedbeanOverflow) — Vocabulary test-sheet automation (collaboration). Turns a NeungyuleVOCA `.docx` + HWP template into an answer key and a seeded fill-in-the-blank `.hwpx` test sheet. `Python`
- [**firstStudy**](https://github.com/hoeongj/firstStudy) — Member-management CRUD REST API (Spring Boot · JPA · Bean Validation · Swagger). Early Spring project.

</details>

---

📫 **Hong Seong-ju** · [@hoeongj](https://github.com/hoeongj) · akftjdwn@gmail.com &nbsp;·&nbsp; 🌐 [Portfolio site](https://hoeongj.github.io/codegate-portfolio/)
