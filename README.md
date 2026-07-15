# 홍성주

백엔드 · 플랫폼 · AI 백엔드 엔지니어를 준비하고 있습니다.

[Portfolio](https://seongju.vercel.app) · [English](README.en.md) · [Email](mailto:akftjdwn@gmail.com) · [solved.ac](https://solved.ac/profile/akftjdwn)

숭실대학교 컴퓨터학부에서 공부하며, 서버 코드에서 끝나지 않고 데이터 수집, 배포, 관측,
실패 복구까지 이어지는 서비스를 만듭니다. 기술의 수보다 문제와 제약, 선택한 이유, 실제로
검증한 결과를 설명할 수 있는 엔지니어링을 지향합니다.

## 대표 작업

### 숭실대 캠퍼스 AI 플랫폼

u-SAINT, LMS, 도서관과 교내 공개 데이터를 웹과 자연어 에이전트, 표준 MCP 도구로 연결한
4개 서비스 시스템입니다. 인증과 사용자별 상태, 승인 후 실행되는 쓰기 도구, 외부 시스템
실패 경계를 설계하고 ARM64 k3s에서 GitOps로 운영합니다.

| 서비스 | 책임 | 링크 |
| --- | --- | --- |
| ssuAI | 사용자 웹과 same-origin BFF | [Live](https://ssuai.vercel.app) · [Repository](https://github.com/ghdtjdwn/ssuAI) |
| ssuMCP | 캠퍼스 데이터와 52개 MCP 도구 | [Repository](https://github.com/ghdtjdwn/ssuMCP) |
| ssuAgent | LangGraph 라우팅, SSE, HITL | [Repository](https://github.com/ghdtjdwn/ssuAgent) |
| ssu-ai-service | 임베딩 요청 격리 게이트웨이 | [Repository](https://github.com/ghdtjdwn/ssu-ai-service) |

[설계·운영·한계가 포함된 사례 보기](https://seongju.vercel.app/projects/ssu-platform/)

### 다른 프로젝트

| 프로젝트 | 직접 맡은 범위 | 근거 |
| --- | --- | --- |
| 그늘 (Geuneul) | PostGIS 공간 검색, 멱등 ETL, Spring/Next.js 서비스와 AWS IaC | [Live](https://geuneul.vercel.app) · [Repository](https://github.com/ghdtjdwn/geuneul) · [Case study](https://seongju.vercel.app/projects/geuneul/) |
| Cham Domi (참 도미) | 모바일 웹 전체와 룸메이트 매칭 백엔드; 인증·기숙사 판정은 팀원 담당 | [Case study](https://seongju.vercel.app/projects/con-dorm/) |
| UNITHON Macro | 음성 주문 클라이언트의 launcher, 설정, 패키징과 키오스크 매크로 통합 | [Repository](https://github.com/UNITHON24/Macro) · [Case study](https://seongju.vercel.app/projects/unithon-macro/) |

더 많은 프로젝트와 기술 기록은 [포트폴리오](https://seongju.vercel.app)에서 볼 수 있습니다.

## 일하는 방식

- API, 데이터, 배포와 관측을 하나의 서비스 경계로 봅니다.
- 인증, 금액, 상태 전이와 외부 시스템 실패는 명시적인 계약으로 분리합니다.
- 성능은 실행 계획, 꼬리 지연과 결과 동일성까지 확인합니다.
- 팀 프로젝트에서는 개인 기여와 팀원의 소유 범위를 구분합니다.
- 결정, 실패한 가설과 검증 결과를 ADR, 작업 로그와 트러블슈팅으로 남깁니다.

## 기술

`Java` · `Kotlin` · `Spring Boot` · `Python` · `FastAPI` · `LangGraph` · `TypeScript` · `Next.js`

`PostgreSQL` · `PostGIS` · `Redis` · `Kafka` · `Docker` · `k3s` · `ArgoCD` · `Terraform` · `AWS`

## 교육·자격

- Soongsil University — Computer Science & Engineering
- 컴퓨터활용능력 2급 (Computer Specialist in Spreadsheet & Database, Level II)
- 정보처리기능사 (Craftsman Information Processing)

연락: [akftjdwn@gmail.com](mailto:akftjdwn@gmail.com)
