# 홍성주 · 숭실대학교 컴퓨터학부 · 백엔드 / 풀스택

> 🇺🇸 English version: [README.en.md](README.en.md) &nbsp;·&nbsp; 🌐 포트폴리오 사이트: <https://ghdtjdwn.github.io/codegate-portfolio/>

프로덕션 멀티서비스 시스템을 직접 **설계 · 구현 · 배포 · 운영**합니다.
대표 작업은 학교 시스템(u-SAINT · LMS · 도서관)을 표준 **MCP 서버**로 노출하고
웹 · 챗봇 · 에이전트로 소비하는 **숭실대 캠퍼스 AI 어시스턴트**입니다.
*Backend / full-stack engineer — I design, build & operate production multi-service systems.*

🟢 **지금 써보기** → **<https://ssuai.vercel.app>** &nbsp;·&nbsp; 🗺️ 여름 생존 지도 → <https://geuneul.vercel.app>

---

## 🏆 대표작 · 숭실대 캠퍼스 AI 어시스턴트 &nbsp;`프로덕션 · 4-서비스`

**🟢 라이브 · MCP 도구 52종 · 4개 서비스 · k3s 멀티포드(HPA·PDB) · GitOps 무중단 롤아웃 · Kafka 이벤트 파이프라인**

학생이 *"오늘 학식 뭐야"*, *"이번 학기 성적 알려줘"*, *"도서관 빈자리 예약해줘"* 를
자연어로 묻고 **행동까지** 시키는 캠퍼스 어시스턴트. 공개 API 문서가 없는 교내 시스템을
브라우저 wire 캡처로 역공학해 붙이고, MCP 표준으로 노출해 웹 · Claude Desktop · ChatGPT
어디서든 쓸 수 있게 만들었다. k3s에 GitOps로 배포 · 운영 중이다.

![ssuAI 대시보드 — 학식 · 시설 · 도서관 · 공지 · u-SAINT/LMS 통합](https://raw.githubusercontent.com/ghdtjdwn/ssuAI/main/docs/assets/dashboard.png)

### 구성 서비스 (4개 저장소 = 하나의 시스템)

| 레포 | 역할 | 핵심 스택 |
|---|---|---|
| **[ssuMCP](https://github.com/ghdtjdwn/ssuMCP)** | MCP 서버 — 캠퍼스 데이터·액션 도구 **52종** | Java 21 · Kotlin · Spring Boot 4 · Spring AI · PostgreSQL/pgvector · Redis/Redisson |
| **[ssuAI](https://github.com/ghdtjdwn/ssuAI)** | 웹 클라이언트 — 5탭 반응형 UI(다크모드) + 자연어 챗봇 | Next.js 16 · TypeScript · TanStack Query · Tailwind/shadcn · Vitest |
| **[ssuAgent](https://github.com/ghdtjdwn/ssuAgent)** | LangGraph 멀티에이전트 — SSE 스트리밍 · HITL | Python · LangGraph · FastAPI · 멀티프로바이더 LLM 폴백 |
| **[ssu-ai-service](https://github.com/ghdtjdwn/ssu-ai-service)** | B2B 임베딩 게이트웨이 — 인증·키 위생 설계, k3s 배포(non-root) | Python · FastAPI |

<details>
<summary><b>아키텍처</b> (펼치기)</summary>

```
브라우저 ── /api/*  ───────────────────────────────────┐
        └─ /api/agent/* ─ proxy ─ ssuAgent ─ MCP ───────┤
                          (LangGraph · SSE · HITL)       ▼
                              ssuMCP (Spring Boot · MCP 서버 · 52 tools)
                                     │  Connector 패턴
                                     ▼
                u-SAINT(SAP) · LMS(Canvas) · 도서관(Pyxis) · 학식 · 공지
```
</details>

### 엔지니어링 하이라이트

- **멀티클라이언트 안전 인증** — 공개/인증 도구를 분리하고 OAuth(Auth0 DCR로 ChatGPT 커넥터 지원) + classic 2-모드. *요청 → 세션 → 클라이언트* 3단계로 세션을 해소해, 웹·Claude Desktop·ChatGPT를 오가도 인증 컨텍스트가 섞이지 않는다.
- **정확한 규정 답변 (하이브리드 RAG)** — 학칙·졸업·장학 답을 근거 문서와 함께 제시(lexical + 벡터 임베딩 **RRF 융합**, pgvector ANN prod 라이브, Testcontainers IT 실증). 무료 임베딩 quota가 *요청 수가 아니라 분당 토큰(TPM)*에 걸린다는 걸 실측하고, resume 가능한 증분 저장으로 나눠 quota 안에서 전체 코퍼스를 완성했다.
- **HITL 예약 에이전트** — 도서관 좌석 예약은 `prepare → 사용자 승인 → confirm` 2단계로만 실행. 동기 타임아웃과 비동기 워커의 이중 상태를 **단일 진실원천 + 멱등 finalize**로 정합화.
- **문서 없는 시스템 역공학** — SAP WebDynpro(u-SAINT)·Pyxis(도서관)를 wire 캡처로 역공학. 추측성 수정을 일정 시점에 끊고 검증된 Rust upstream(rusaint)을 UniFFI로 통합한 **빌드 vs 도입 판단**.
- **멀티포드 HA + Kafka 이벤트 파이프라인** — 실사용 규모는 작지만, 전교생 규모를 가정해 프로덕션 운영 관행을 그대로 적용했다. 프론트/백엔드 replicas=2(HPA·PDB), stateful MCP 세션은 Traefik 쿠키 sticky로 포드 고정, 툴콜·예약 알림을 인메모리에서 **Kafka 이벤트 스트림으로 분리**(fail-open 비차단 프로듀서 — 브로커가 죽어도 요청은 통과). 롤링 배포 무중단 + **라이브 fail-open 드릴**(브로커 다운 중에도 툴콜 HTTP200)로 검증.
- **운영 · 품질 (prod)** — ArgoCD Image Updater GitOps 무중단 롤아웃, Helm, GitHub Actions(action SHA-pin), Testcontainers + JaCoCo 커버리지 게이트, OpenTelemetry/Grafana 관측성(RED·Kafka 대시보드 · Prometheus 알림 룰), gitleaks · pod-security 하드닝. *실험/랩: Cilium eBPF FQDN 이그레스(kind 랩 실증) · n8n 운영 자동화.*
- **디버깅 기록** → [트러블슈팅 하이라이트](https://github.com/ghdtjdwn/ssuMCP/blob/main/docs/troubleshooting-highlights.md): *틀린 가설 → 실제 원인 → 해결*로 정리한 사례집.

`Java 21` · `Kotlin` · `Spring Boot 4` · `Spring AI` · `Python` · `LangGraph` · `FastAPI` · `TypeScript` · `Next.js 16`
`PostgreSQL` · `pgvector` · `Redis / Redisson` · `Kafka` · `k3s` · `ArgoCD` · `Helm` · `GitHub Actions` · `Grafana` · `Testcontainers`

---

## 📂 그 외 프로젝트

### 🗺️ [그늘 (Geuneul)](https://github.com/ghdtjdwn/geuneul) — 여름 생존 지도 &nbsp; 🟢 [Live](https://geuneul.vercel.app)
폭염·장마 때 지도에 위치만이 아니라 **"지금 앉을 수 있는지·시원한지·붐비는지"** 를 최근 제보로 답하는 생활 생존 지도. **PostGIS**로 전국 공공데이터(공중화장실 5.2만·도서관 3.5천 등)를 반경(`ST_DWithin`)·최근접(kNN `<->`)·bounds로 검색하고, 유효 제보를 최근성 × 신뢰도로 집계한 `survival_score` SQL 뷰로 마커를 3색 랭킹 + 시나리오 2단 재랭킹. 브라우저는 항상 동일 오리진 `/api/*` BFF만 호출(CORS·ALB 제약 동시 회피).
`Spring Boot 4 · Java 21 · Next.js · PostGIS · Redis · AWS ECS Fargate · Terraform(IaC) · GitHub Actions OIDC · ECR/ALB/CloudFront`

### 🏠 [con-dorm](https://github.com/con-dorm) — 기숙사 룸메이트 매칭 &nbsp;`팀 프로젝트`
생활성향 기반 가중치 궁합 점수로 룸메이트를 매칭하고 기숙사 합격을 예측하는 모바일 앱. 프론트/백엔드가 **API 계약서** 하나로 분리 개발.
[프론트엔드(Next.js 16 · React 19)](https://github.com/con-dorm/FE) · [백엔드(Java)](https://github.com/con-dorm/BE) · [조직 문서](https://github.com/con-dorm)

### 🔊 [Macro](https://github.com/UNITHON24/Macro) — 음성 키오스크 주문 보조 &nbsp;`UNITHON 2024 해커톤`
키오스크 화면을 **EasyOCR**로 읽어 좌표를 인덱싱하고, **음성 명령**으로 메뉴를 대신 눌러주는 접근성 보조 도구. 입력/출력/주문 허브를 분리한 소켓 서버 구조.
`Python · 음성인식(STT) · EasyOCR · TTS · 화면 자동화`

### 🎮 두근두근 자료구조 (DDSC) — AI 학습 미연시 &nbsp;`공모전 · 팀 프로젝트(비공개)`
소꿉친구 지수가 자료구조를 가르치고, 학습자의 **자유서술 답을 AI가 채점**해 호감도가 오르는 비주얼노벨. 문제·정답·대사는 사람이 쓰고 **AI는 채점만** → 잘못 가르칠 위험 0. 호감도·비트 순서·엔딩 등 게임 무결성은 엔진(코드)이 소유해 AI가 오작동해도 안 깨진다. 숭실대 AX 인터랙티브 콘텐츠 공모전 출품작.
`JavaScript · LLM 채점(Claude/OpenAI, mock 폴백)`

### 🎓 [cs-coursework](https://github.com/ghdtjdwn/cs-coursework) — 학부 전공 과제 포트폴리오 &nbsp;`12과목`
학부 전공 수업 결과물을 과목별로 정리. **시스템 프로그래밍**(C, ~2,300줄 `fdupes` 류 중복파일 도구) · **프로그래밍 언어론**(렉서 → 재귀하향 파서 → AST 트리워킹 인터프리터) · **컴퓨터 구조**(RISC-V 디스어셈블러·시뮬레이터) · **인공지능**(Transformer·ViT·BERT from scratch) · **알고리즘**(이동 의미론 정렬) · **네트워크**(TLS·asyncio·ZeroMQ) 등.
`C · C++ · Java · Python · PyTorch · Jupyter`

<details>
<summary>🌱 그 외 (초기 학습 · 협업 소도구)</summary>

- [**RedbeanOverflow**](https://github.com/kwon32/RedbeanOverflow) — 단어 시험지 자동화 (협업). 능률VOCA `.docx` + HWP 템플릿 → 답지·빈칸 시험지 `.hwpx` 시드 기반 자동 생성 CLI. `Python`
- [**firstStudy**](https://github.com/ghdtjdwn/firstStudy) — 회원 관리 CRUD REST API (Spring Boot · JPA · Bean Validation · Swagger). Spring 입문기.

</details>

---

📫 **홍성주** · [@ghdtjdwn](https://github.com/ghdtjdwn) · akftjdwn@gmail.com &nbsp;·&nbsp; 🌐 [포트폴리오 사이트](https://ghdtjdwn.github.io/codegate-portfolio/)
