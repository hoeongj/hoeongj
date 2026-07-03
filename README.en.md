### Soongsil University, School of Computer Science & Engineering · Backend / Full-stack

**한국어** [README.md](README.md) · **English** (this document)

I **design, build, deploy, and operate** production multi-service systems end to end.
My flagship work is a **campus AI assistant for Soongsil University** that exposes the school's
systems (u-SAINT · LMS · library) as a standards-compliant **MCP server**, consumed by a web app,
a chatbot, and agents.

🟢 **Live** — chatbot/dashboard <https://ssuai.vercel.app> · MCP server `https://ssumcp.duckdns.org/mcp`

---

## Soongsil Campus AI Assistant

A campus assistant students can ask in natural language — *"What's for lunch today?"*,
*"Show me this semester's grades"*, *"Reserve a library seat for me"* — and have it **take action**,
not just answer. Campus systems with no public API documentation were reverse-engineered from
browser wire captures and exposed over the MCP standard, so it works from the web app, Claude
Desktop, or ChatGPT alike. Deployed and operated on k3s with zero-downtime GitOps.

![ssuAI dashboard — cafeteria · facilities · library · notices · u-SAINT/LMS integration](https://raw.githubusercontent.com/hoeongj/ssuAI/main/docs/assets/dashboard.png)

```
Browser ── /api/* ──────────────────────────────────────┐
        └─ /api/agent/* ─ proxy ─ ssuAgent ─ MCP ───────┤
                          (LangGraph · SSE · HITL)      ▼
                              ssuMCP (Spring Boot · MCP server · 52 tools)
                                     │  Connector pattern
                                     ▼
                u-SAINT (SAP) · LMS (Canvas) · Library (Pyxis) · Cafeteria · Notices
```

### Services

| Repo | Role | Core stack |
|---|---|---|
| **[ssuMCP](https://github.com/hoeongj/ssuMCP)** | MCP server — **52 tools** for campus data & actions | Java 21 · Kotlin · Spring Boot 4 · Spring AI · PostgreSQL/pgvector · Redis/Redisson |
| **[ssuAI](https://github.com/hoeongj/ssuAI)** | Web client — 5-tab responsive UI (dark mode) + natural-language chatbot | Next.js 16 · TypeScript · TanStack Query · Tailwind/shadcn · Vitest |
| **[ssuAgent](https://github.com/hoeongj/ssuAgent)** | LangGraph multi-agent — SSE streaming · HITL | Python · LangGraph · FastAPI · multi-provider LLM fallback |
| **[ssu-ai-service](https://github.com/hoeongj/ssu-ai-service)** | B2B embedding gateway — auth & key-hygiene design, k3s deployment (non-root) | Python · FastAPI |

### Engineering Highlights

- **MCP server (52 tools)** — public vs. authenticated tool separation, dual-mode auth (OAuth with Auth0 DCR for the ChatGPT connector, plus classic), and **3-tier session resolution** for safe authentication in a multi-client environment.
- **Hybrid RAG** — answers about academic regulations, graduation, and scholarships are grounded by **RRF fusion** of lexical and vector-embedding retrieval (pgvector ANN profile live in prod, proven with Testcontainers integration tests). Measured the free embedding quota empirically as *TPM rather than request count* and fully ingested the corpus via incremental persistence.
- **HITL reservation agent** — library seat reservations execute only through a two-step `prepare → user approval → confirm` flow. Resolved the dual state between the synchronous timeout and the asynchronous worker with a **single source of truth + idempotent finalize**.
- **Reverse-engineering undocumented systems** — SAP WebDynpro (u-SAINT) and Pyxis (library) reverse-engineered from wire captures. A deliberate **build-vs-buy call**: cut off speculative patching at a fixed point and integrated the proven Rust upstream (rusaint) via UniFFI.
- **Operations & quality** — zero-downtime GitOps on k3s with ArgoCD Image Updater, Helm, GitHub Actions (SHA-pinned actions), Testcontainers + JaCoCo coverage gate, OpenTelemetry/Grafana observability (RED dashboard · Prometheus alert rules), n8n ops automation, gitleaks · pod-security hardening.
- **Debugging log** → [Troubleshooting highlights](https://github.com/hoeongj/ssuMCP/blob/main/docs/troubleshooting-highlights.md): 8 major cases, each written up as *wrong hypothesis → actual cause → fix*.

### Tech Stack

`Java 21` · `Kotlin` · `Spring Boot 4` · `Spring AI` · `Python` · `LangGraph` · `FastAPI` · `TypeScript` · `Next.js 16`
`PostgreSQL` · `pgvector` · `Redis / Redisson` · `Docker` · `k3s` · `ArgoCD` · `Helm` · `GitHub Actions` · `Vercel`
`OpenTelemetry` · `Grafana` · `Testcontainers`

---

📫 **hoengj** · akftjdwn@gmail.com
