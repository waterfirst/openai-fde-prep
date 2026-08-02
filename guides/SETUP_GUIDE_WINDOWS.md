# 변호사 노트북 세팅 & 파산업무 실행 매뉴얼 (Windows · PowerShell)

> **대상:** 소프트웨어를 전혀 모르는 현장 전문가(변호사).
> **목표:** 복붙만 하면 `로컬 Python 리더 + Codex + 법제처 MCP`가 노트북에 깔리고,
> 개인파산/개인회생 사건 1차 검토 패킷을 스스로 뽑을 수 있게 된다.
> **환경:** Windows 10/11, PowerShell. (Mac은 명령만 다르고 순서는 동일)

---

## ⭐ 큰 그림 (왜 이렇게 하나)

> **Python 리더(눈, 노트북 안에서 마스킹) → Codex(손, 마스킹본으로 패킷 생성) → 법제처 MCP(근거) → 변호사(최종판단)**

핵심 원칙 3줄:
1. **실제 사건 원문은 노트북 밖으로 나가지 않는다.** Python이 먼저 개인정보를 가린다.
2. **AI 산출물은 초안**이다. 최종 법률판단·제출문구는 변호사 책임.
3. **법령은 지어내지 않고** 법제처(law.go.kr) MCP로 조회한다. 못 찾으면 "확인 불가".

> ⚠️ **초보자 제1 함정:** 뭔가 설치한 뒤엔 **PowerShell을 껐다 다시 켜야** 새 명령이 인식된다.
> "인식되지 않습니다" 에러가 뜨면 십중팔구 이것 — 새 창을 열자.

---

# PART 1 — 컴퓨터 환경 셋업

## 0단계 — ChatGPT 구독 (브라우저)
- [chatgpt.com](https://chatgpt.com) → **Plus 이상 결제**. Codex CLI가 이 계정으로 로그인한다.

## 1단계 — PowerShell을 관리자로 열기
- 시작 버튼 → `PowerShell` 검색 → **"관리자 권한으로 실행"**.
- 설치 도구(winget) 확인:
```powershell
winget --version
```
> 버전이 안 나오면 Microsoft Store에서 **"앱 설치 관리자(App Installer)"** 설치 후 다시.

## 2단계 — 런타임 4개 설치
아래 4줄을 순서대로 (라이선스 물으면 `Y`):
```powershell
winget install -e --id OpenJS.NodeJS.LTS
winget install -e --id Python.Python.3.12
winget install -e --id Git.Git
winget install -e --id Microsoft.VisualStudioCode
```
> `OpenJS.NodeJS.LTS`가 가장 중요 — Codex와 법제처 MCP 둘 다 이게 없으면 안 돌아간다.

**설치가 끝나면 PowerShell 창을 닫고 새로 연다.**

## 3단계 — 설치 확인
```powershell
node -v
npm -v
python --version
git --version
```
> 4개 다 버전 숫자가 나오면 통과. `python`이 Microsoft Store 창을 열면 2단계 Python 설치 실패 → 다시.

## 4단계 — Codex CLI 설치 + 로그인
```powershell
npm install -g @openai/codex
```
설치 후 **새 창을 열고**:
```powershell
codex --version
codex login
```
> 브라우저가 열리면 0단계 ChatGPT 계정으로 로그인. 창을 닫고 PowerShell로 돌아온다.

## 5단계 — 법제처 인증키(OC) 발급 (브라우저)
- [open.law.go.kr](https://open.law.go.kr) 가입 → OpenAPI 신청 → **인증키(OC)** 즉시 발급.
- 키를 메모장에 복사해둔다.

## 6단계 — 법제처 MCP 등록
설정 폴더를 만들고 파일을 VS Code로 연다:
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex" | Out-Null
code "$env:USERPROFILE\.codex\config.toml"
```
열린 파일에 아래를 붙여넣고, `LAW_OC` 값만 5단계 키로 바꾼 뒤 **`Ctrl+S` 저장**:
```toml
[mcp_servers.korean_law]
command = "npx"
args = ["-y", "korean-law-mcp"]
env = { LAW_OC = "여기에_발급받은_키_붙여넣기" }
startup_timeout_sec = 30
tool_timeout_sec = 180
enabled = true
```

## 7단계 — 연결 테스트 (성공 기준)
```powershell
codex
```
Codex 창에 입력:
```
개인정보보호법 제15조를 korean_law MCP로 찾아서 조문 원문과 쉬운 설명을 함께 보여줘.
```
> **실제 조문**이 나오면 근거 연결 성공 ✅. 지어내면 실패 → 6단계 키/저장 재확인.

## 8단계 — 저장소 받기
Codex에서 나와서(`/exit`), 문서 폴더로 이동 후:
```powershell
cd $env:USERPROFILE\Documents
git clone https://github.com/waterfirst/fde-codex-lawyer.git
cd fde-codex-lawyer
code .
```

---

# PART 2 — 파산업무 프로세스 실행

> 아래 명령은 모두 **`fde-codex-lawyer` 폴더 안에서** 실행한다.
> Codex도 이 폴더에서 켜야 `AGENTS.md`/`SKILL.md`를 읽는다.

## 준비 — PDF 읽기 라이브러리 (실제 사건 대비)
```powershell
pip install pypdf
```

## A단계 — Python 리더로 첫 성공 (샘플 마스킹)
```powershell
python scripts\local_doc_intake.py cases\sample_001_personal_bankruptcy\input --out cases\sample_001_personal_bankruptcy\output\local_intake
```
> 끝에 `{"status": "ok", ... "files": N}`가 뜨면 성공.

## B단계 — 마스킹 결과 눈으로 확인 (⭐ 보안 시연)
```powershell
code cases\sample_001_personal_bankruptcy\output\local_intake
```
- `redacted_text.md` — 주민번호·이름·계좌가 `[REDACTED_…]`로 가려짐
- `case_intake.json` / `document_index.csv` — 구조화 결과
> "원문은 노트북 밖으로 안 나간다. Codex는 이 가려진 결과만 본다."

## C단계 — Codex 켜고 법제처 연결 확인
```powershell
codex
```
붙여넣기:
```
채무자 회생 및 파산에 관한 법률에서 개인파산 관련 조문을 korean_law MCP로 찾아줘.
조문 번호, 핵심 문장, 쉬운 설명을 짧게 정리해줘.
```

## D단계 — Intake 빠른 성공
Codex에 붙여넣기:
```
AGENTS.md와 SKILL.md를 먼저 읽어라.
cases/sample_001_personal_bankruptcy/output/local_intake/ 폴더의
case_intake.json, document_index.csv, redacted_text.md만 사용해
사건 1차 정리를 해라.
- 문서 인덱스, 누락자료 목록, 사건 요약 초안을 만들어라.
- 추정 금지, 자료 없으면 "자료 없음".
- 개인정보 복원 금지, 법률 판단 확정 금지.
```

## E단계 — 전체 검토 패킷 생성 (⭐ 메인 산출물)
Codex에 붙여넣기:
```
AGENTS.md와 SKILL.md를 먼저 읽어라.
prompts/04_full_orchestration_prompt.md의 지시대로
cases/sample_001_personal_bankruptcy 사건을 처리해
변호사 검토용 1차 패킷을 만들어라.

산출물(cases/sample_001_personal_bankruptcy/output/ 아래):
- 01_case_summary.md
- 02_issue_checklist.md
- 03_draft_report.md
- 04_evidence_table.csv
- 05_questions_for_lawyer.md
- review_packet.html

규칙: 법률 판단 확정 금지, 개인정보 원문 출력 금지,
법령 근거는 korean_law MCP로 확인, 근거 없는 문장은 unsupported_claims.md로 분리.
```

## F단계 — 결과 열어보기
Codex에서 나와서(`/exit`):
```powershell
start cases\sample_001_personal_bankruptcy\output\review_packet.html
code cases\sample_001_personal_bankruptcy\output
```

## G단계 — 실제(익명) 사건에 재사용
1. 새 사건 폴더:
```powershell
New-Item -ItemType Directory -Force -Path cases\2026\2026-08-001_pilot\input\raw_pdfs, cases\2026\2026-08-001_pilot\input\interview
```
2. 실제 PDF/상담메모를 `input\raw_pdfs\`, `input\interview\`에 복사 (노트북 안에서만).
3. **Python 리더로 먼저 마스킹:**
```powershell
python scripts\local_doc_intake.py cases\2026\2026-08-001_pilot\input --out cases\2026\2026-08-001_pilot\output\local_intake
```
4. Codex엔 **오직 `output\local_intake\` 마스킹본만** 넘긴다 (D·E 프롬프트의 경로를 이 폴더로 교체).

> 실제 사건 절대 규칙: **외부/Codex에 넘기기 전 항상 리더로 마스킹 → `redacted_text.md`를 눈으로 확인.**

## H단계 — 핸드오프
1. 오늘 만든 `output\` 패킷 = "다음에도 이렇게 나온다"는 증거.
2. `AGENTS.md`(안전규칙) + `SKILL.md`(처리절차) + `prompts\copy_paste.md`(복붙문구) 위치 공유.
3. **다음 실제 익명 사건 1건 선정** → 처리시간·수정량 측정.
4. 사건 유형이 늘면 **자신만의 `skill_*.md`를 계속 추가**한다 (예: 사업자 파산, 상속 얽힌 회생 등).

---

## 🔧 흔한 에러 & 해결

| 증상 | 원인 | 해결 |
|---|---|---|
| `... 인식되지 않습니다` | 설치 후 PATH 미갱신 | PowerShell 새 창 열기 |
| `python`이 Store 창을 염 | Python 설치 실패 | 2단계 재설치 |
| Codex가 조문을 지어냄 | MCP 미연결 | 6단계 `config.toml` 키·저장 확인, Codex 재시작 |
| `npx korean-law-mcp` 오류 | Node 미설치/구버전 | `node -v`(v20+) 확인, 재설치 |
| PDF 텍스트 안 나옴 | pypdf 미설치 | `pip install pypdf` |

---

## 🔒 계속 강조할 3줄
1. AI 산출물은 **초안**, 최종 판단은 변호사 책임.
2. 원문은 **노트북 밖으로 안 나감** — Codex는 마스킹본만.
3. 법령은 **법제처 MCP로 조회**, 못 찾으면 "확인 불가".

---

_이 매뉴얼은 도구 설치·운영 절차이며 법률 자문이 아니다. 모든 결론은 인용된 조문·판례로 검증하고 변호사가 최종 책임진다._
