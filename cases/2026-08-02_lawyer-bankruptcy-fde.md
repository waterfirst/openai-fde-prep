# 사례 — 파산전문 변호사 2시간 온사이트 FDE 코칭

_2026-08-02 · 고객: 개인파산·개인회생 전문 변호사 · 결과: 성공(재사용 자산 + 자립 신호)_

관련 산출물:
- 실행 매뉴얼: [`guides/SETUP_GUIDE_WINDOWS.md`](../guides/SETUP_GUIDE_WINDOWS.md)
- 프로젝트 저장소: https://github.com/waterfirst/fde-codex-lawyer

---

## 1. 문제 (Problem)

- 고객은 월 **12건** 규모의 개인파산/개인회생 사건을 처리하는 변호사.
- 병목 = **반복적인 문서 검토·정리**(채권자·재산·소득·누락자료 파악)에 시간 소모.
- 제약 = **개인정보가 극도로 민감**(주민번호·계좌·가족관계). 클라우드 업로드가 곧 리스크.
- 고객은 **소프트웨어를 전혀 모름.**

## 2. 재정의 (Reframe)

> "AI에게 법률 판단을 넘기는 게 아니다.
> 반복 정리 업무를 줄이고, **변호사가 판단할 지점을 더 빨리 보이게** 만든다."

이 한 문장이 프로젝트 전체의 안전선과 성공기준을 결정함. 법률 판단은 고객 몫으로 명확히 분리.

## 3. 접근 — 도구 분업 아키텍처 (Approach)

| 주체 | 역할 |
|---|---|
| **Python 로컬 리더 (눈)** | 노트북 안에서 문서 읽기 + 개인정보 마스킹 + 날짜/금액/문서목록 구조화 |
| **Codex (손)** | 마스킹본만 받아 요약/쟁점/증빙표/HTML 패킷 생성 |
| **법제처 MCP (근거)** | 「채무자 회생 및 파산에 관한 법률」 조문을 실제 조회 (환각 차단) |
| **변호사 (판단)** | 법률의견·제출문구 확정 |

파이프라인: **로컬 마스킹 → Codex 패킷 생성 → MCP 근거확인 → 변호사 검토**

## 4. 개인정보 격리 — 핵심 설계 (Privacy Architecture)

민감정보 고객이라 **원격 코칭을 데이터 격리형으로 설계**:

- **사건 원문 = 변호사 노트북에만.** 절대 기기 밖으로 안 나감.
- **지시(프롬프트·명령) = 텔레그램으로만.** 내가 원격에서 지시문을 보내고, 실행은 전부 고객 기기 안에서.
- 즉 채널을 흐른 건 **"무엇을 하라"는 명령뿐**, 사건 데이터가 아님.

> 단일 실패지점(면접에서 강조): 고객이 사건 스니펫(마스킹본이라도)을 텔레그램에 붙여넣는 순간 모델이 깨진다.
> 각인 규칙 → **"채널엔 시키는 말만, 사건은 기기 안에서만."**

## 5. 결과 (Result)

- 2시간 안에 **환경설치(0~8단계) → 샘플 사건 1차 검토 패킷 생성**까지 완주.
- 산출물: `01_case_summary` / `02_issue_checklist` / `03_draft_report` / `04_evidence_table.csv` / `05_questions_for_lawyer` / `review_packet.html`.
- 재사용 자산: `AGENTS.md`(안전규칙) + `SKILL.md`(파산 검토 절차) + `prompts/`(복붙) + 샘플 사건 구조.

### 고객 피드백
- **"만족스럽다."**
- **"사용하면서 나만의 사건 분석 `skill.md`를 많이 만들어야겠다."**
  → 도구 의존이 아니라 **자립(self-authoring)** 신호. FDE 성공의 진짜 지표.

## 6. 배운 것 (Lessons for FDE)

1. **재정의가 안전선을 만든다.** "판단 아님, 정리다" 한 줄이 개인정보·법률단정·근거 규칙을 전부 파생시켰다.
2. **첫 성공은 로컬에서 잡는다.** 외부 클라우드 데모가 아니라 고객 기기 안 Python 첫 실행에서 신뢰가 생겼다.
3. **환각 차단이 곧 신뢰.** 법령을 MCP로 조회하고 못 찾으면 "확인 불가"라고 말하게 한 것이 변호사에게 가장 설득력 있었다.
4. **자산화가 임팩트를 남긴다.** 떠난 뒤 남는 문서/프롬프트/폴더 구조가 "다음 사건에도 쓴다"를 만든다.
5. **준비된 저장소가 속도를 만든다.** 사전 제작한 GitHub 템플릿 덕에 2시간에 압축 가능했다.

## 7. 다음 측정 (Next / Metrics)

실제 익명 사건 1건을 파일럿으로:
- 사건당 **처리시간** 단축률
- **누락자료 발견률**
- 변호사 **수정량**(AI 초안 → 최종)
- 루틴 **재사용률**

---

## Interview talking points (EN, STAR)

- **Situation:** A solo bankruptcy lawyer, ~12 cases/month, bottlenecked on repetitive document review, with extreme PII sensitivity and zero software background.
- **Task:** In 2 hours, stand up a repeatable case-intake workflow that keeps client data on the lawyer's own device.
- **Action:** Reframed the goal from "AI decides" to "AI organizes so the lawyer decides faster." Built a tool-division pipeline — a local Python reader (reads + redacts PII on-device), Codex (turns the redacted output into a review packet), and a law.go.kr MCP (grounds every citation, says "not found" instead of hallucinating). Ran the whole engagement remotely with a strict **instruction-over-Telegram, data-on-device** separation.
- **Result:** Full working packet in one session; the client asked to start authoring their **own** case-analysis skills — a self-sufficiency signal. Left reusable assets (AGENTS/SKILL/prompts/sample case) and a beginner PowerShell manual.
- **Lesson:** In regulated, high-sensitivity domains, the FDE win is a **data-isolated, grounded, self-extending** system — not a demo.

_이 문서는 FDE 준비용 사례 기록이며 법률 자문이 아니다._
