# Hong Seong Ju

I am preparing for backend, platform, and AI backend engineering roles.

[Portfolio](https://seongju.vercel.app/en/) · [한국어](README.md) · [Email](mailto:akftjdwn@gmail.com) · [solved.ac](https://solved.ac/profile/akftjdwn)

I study Computer Science & Engineering at Soongsil University. I build services beyond the
application layer, including data ingestion, deployment, observability, and recovery. I value
engineering that makes the problem, constraints, trade-offs, and verified results explainable.

## Selected work

### Soongsil Campus AI Platform

A four-service system connecting u-SAINT, LMS, the university library, and public campus data to
the web, natural-language agents, and standard MCP tools. I designed authentication and per-user
state boundaries, approval-gated write operations, and external-system failure handling, and I
operate the platform on ARM64 k3s through GitOps.

| Service | Responsibility | Links |
| --- | --- | --- |
| ssuAI | User-facing web app and same-origin BFF | [Live](https://ssuai.vercel.app) · [Repository](https://github.com/ghdtjdwn/ssuAI) |
| ssuMCP | Campus data and 52 MCP tools | [Repository](https://github.com/ghdtjdwn/ssuMCP) |
| ssuAgent | LangGraph routing, SSE, and HITL | [Repository](https://github.com/ghdtjdwn/ssuAgent) |
| ssu-ai-service | Isolated embedding gateway | [Repository](https://github.com/ghdtjdwn/ssu-ai-service) |

[Read the case study, including design, operations, and limitations](https://seongju.vercel.app/en/projects/ssu-platform/)

### Other projects

| Project | My scope | Evidence |
| --- | --- | --- |
| Geuneul | PostGIS spatial search, idempotent ETL, Spring/Next.js services, and AWS IaC | [Live](https://geuneul.vercel.app) · [Repository](https://github.com/ghdtjdwn/geuneul) · [Case study](https://seongju.vercel.app/en/projects/geuneul/) |
| Cham Domi | Entire mobile web frontend and roommate-matching backend; authentication and dorm eligibility are teammate-owned | [Case study](https://seongju.vercel.app/en/projects/con-dorm/) |
| UNITHON Macro | Launcher, configuration, packaging, and kiosk-macro integration for the voice-order client | [Repository](https://github.com/UNITHON24/Macro) · [Case study](https://seongju.vercel.app/en/projects/unithon-macro/) |

See the [portfolio](https://seongju.vercel.app/en/) for more projects and engineering records.

## Engineering practice

- Treat APIs, data, deployment, and observability as one service boundary.
- Model authentication, money, state transitions, and external failures as explicit contracts.
- Validate performance with plans, tail latency, and result equivalence.
- Separate my contribution from teammate-owned work.
- Preserve decisions, failed hypotheses, and validation in ADRs, work logs, and troubleshooting records.

## Technology

`Java` · `Kotlin` · `Spring Boot` · `Python` · `FastAPI` · `LangGraph` · `TypeScript` · `Next.js`

`PostgreSQL` · `PostGIS` · `Redis` · `Kafka` · `Docker` · `k3s` · `ArgoCD` · `Terraform` · `AWS`

## Education and credentials

- Soongsil University — Computer Science & Engineering
- 컴퓨터활용능력 2급 (Computer Specialist in Spreadsheet & Database, Level II)
- 정보처리기능사 (Craftsman Information Processing)

Contact: [akftjdwn@gmail.com](mailto:akftjdwn@gmail.com)
