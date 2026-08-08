# Forward Deployed Engineer — Portfolio

애매한 현장 문제를 검증된 반복 시스템으로. A portfolio of forward-deployed work:
taking a real customer's messy problem and shipping a working, safe, reusable system.

**Live:** https://waterfirst.github.io/openai-fde-prep/

> I turn ambiguous real-world problems into validated, repeatable systems.

## How I work

Reframe the problem → ship the minimum working system → divide the tools
(local pipeline = eyes, LLM = hands, MCP = grounding, human = final call) →
guardrail first (PII isolation, no hallucinated facts, human-in-the-loop) →
measure adoption → hand off reusable assets.

## Selected work

- **파산 변호사 사건검토 워크플로우** — 로컬 마스킹 → Codex 검토패킷 → 법제처 MCP 근거. 원문은 고객 기기를 벗어나지 않음.
  [repo](https://github.com/waterfirst/fde-codex-lawyer) · [case study](cases/2026-08-02_lawyer-bankruptcy-fde.md)
- **KIS 게이트형 자율 워크플로우** — eval-gate·리스크 가드레일 기반 자율 의사결정, 500일 검증 루프.
- **전국 회생법원 매각공고 MCP** — 공공데이터를 질의 가능한 도구로. ([repo](https://github.com/waterfirst/nationwide-rehab-sale-watch-mcp))
- **OLED 수명 분석 웹앱** — 물리 기반 도메인 워크플로우 자동화.
- **Telegram + Codex 오케스트레이션** — human-in-the-loop 다중 에이전트 조율.
- **연구개발 AI 세미나** — 현장 엔지니어 역량이식(enablement).

## Case studies

- [파산전문 변호사 2시간 온사이트 FDE 코칭](cases/2026-08-02_lawyer-bankruptcy-fde.md)
  — 개인정보 격리(지시-데이터 분리) · 로컬 마스킹 파이프라인 · 고객 자립 신호 · STAR 요약.

## Guides

- [비전문가용 로컬 AI 워크플로우 셋업 (Windows·PowerShell)](guides/SETUP_GUIDE_WINDOWS.md)
  — SW 비전문가가 복붙만으로 환경설치부터 실무 실행까지.
- [AI 에이전트를 활용한 역사학 논문 연구 가이드](guides/AI_AGENT_HISTORY_THESIS_GUIDE.md)
  — 주장 원자화·사료카드·경쟁가설·독립 검토를 거쳐 학생에게 연구 주도권을 인계하는 워크플로우.
