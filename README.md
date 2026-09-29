# Iyuno AI Agent Portfolio

> **Real Job Posting → Real GitHub Project**

Iyuno의 **AI Agent Engineer** 채용공고를 기반으로 설계한
LLM Agent + RAG + Tool Calling + Multi-step Workflow 프로젝트입니다.

단순한 LLM Chatbot이 아니라, 문서 검색부터 외부 Tool 호출, 상태 관리, 평가 및 Feedback까지 포함하는 **E2E AI Agent 시스템** 구현을 목표로 합니다.

---

## 1. Project Overview

### 프로젝트 목적

공개된 기술·보안 문서를 기반으로 사용자의 질문에 답변하고, 필요한 경우 외부 Tool을 호출하여 여러 단계를 거쳐 문제를 해결하는 AI Agent를 구현합니다.

Agent는 다음과 같은 흐름으로 동작합니다.

```text
User Question
      │
      ▼
┌──────────────┐
│ Agent Router │
└──────┬───────┘
       │
       ├───────────────┐
       ▼               ▼
   RAG Search       Tool Calling
       │               │
       └───────┬───────┘
               ▼
       Multi-step Workflow
               │
               ▼
        Answer + Citation
               │
               ▼
          User Feedback
               │
               ▼
        Evaluation / Logs
```

---

## 2. Why This Project?

이 프로젝트는 실제 AI Agent Engineer 채용공고의 요구사항을 GitHub에서 확인할 수 있는 구현 증거로 변환하는 것을 목표로 합니다.

단순히 LLM API를 호출하는 수준을 넘어 다음 기능을 구현합니다.

* LLM 기반 Agent
* RAG 기반 문서 검색
* 검색 결과 Citation
* Tool Calling
* API orchestration
* Multi-step workflow
* Agent State 관리
* Context 관리
* Evaluation
* Feedback Loop
* API / Database 통합
* Latency / Cost / Reliability 측정

---

## 3. Job Requirement Mapping

| Iyuno 요구사항                       | 구현 내용                                               | Evidence                     |
| -------------------------------- | --------------------------------------------------- | ---------------------------- |
| LLM 기반 AI Agent 시스템 설계 및 개발      | Agent workflow 및 LLM integration                    | `app/agent/`                 |
| RAG 기반 정보 검색 및 응답 시스템            | 문서 ingestion → embedding → vector search → citation | `app/rag/`                   |
| Tool calling / API orchestration | Calculator / Search / Policy API                    | `app/tools/`                 |
| Multi-step workflow              | Agent가 여러 단계를 순차적으로 실행                              | `app/agent/graph.py`         |
| State / Context 관리               | 실행 상태 및 대화 context 관리                               | `state.py`, `context.py`     |
| Evaluation / Feedback Loop       | 평가 데이터, metric, feedback 저장                         | `evaluation/`, `feedback.py` |
| API / DB 통합                      | FastAPI + Database 연동                               | `app/api/`, `app/db/`        |
| Latency / Cost / Reliability 개선  | 실행 로그 및 metric 측정                                   | `evaluation/metrics.py`      |
| 실패 케이스 분석                        | hallucination / tool failure / context loss 분석      | `docs/failure-cases.md`      |

---

## 4. Core Features

### 4.1 RAG

공개 기술 문서를 수집하고 다음 과정을 거쳐 검색 가능한 형태로 변환합니다.

```text
Documents
   ↓
Loading
   ↓
Chunking
   ↓
Embedding
   ↓
Vector Store
   ↓
Retriever
   ↓
Relevant Context
```

검색된 문서를 기반으로 답변을 생성하며, 답변에 사용된 문서의 출처를 함께 표시합니다.

### 4.2 Tool Calling

Agent가 질문을 분석한 후 필요한 Tool을 선택하여 호출합니다.

현재 구현 예정 Tool:

* Calculator
* Document Search
* Policy / Information API

예:

```text
User
 ↓
"이 계산을 해줘"
 ↓
Agent
 ↓
Calculator Tool
 ↓
Result
 ↓
LLM
 ↓
Final Answer
```

---

## 5. Multi-step Agent Workflow

Agent는 하나의 LLM 호출로 모든 문제를 해결하지 않고 필요한 작업을 여러 단계로 나누어 처리합니다.

예시:

```text
질문 입력
   ↓
질문 분석
   ↓
필요한 정보 검색
   ↓
검색 결과 검증
   ↓
필요한 Tool 호출
   ↓
결과 통합
   ↓
최종 답변 생성
   ↓
Citation 표시
```

Agent의 실행 과정은 State를 통해 관리합니다.

예시 State:

```python
{
    "question": "...",
    "context": [...],
    "retrieved_documents": [...],
    "tool_results": [...],
    "answer": "...",
    "citations": [...],
    "error": None
}
```

---

## 6. Feedback Loop

사용자가 Agent의 답변에 대해 Feedback을 남길 수 있도록 구성합니다.

```text
Agent Answer
     ↓
User Feedback
     ↓
Feedback Storage
     ↓
Error Analysis
     ↓
Prompt / Retrieval / Tool 개선
     ↓
Evaluation
```

Feedback 데이터는 이후 Agent 개선 및 실패 케이스 분석에 활용합니다.

---

## 7. Evaluation

Agent의 성능을 정량적으로 평가하기 위해 별도의 evaluation dataset을 구성합니다.

### Evaluation Dataset

최소 30개의 질문을 작성합니다.

질문 유형 예시:

* 단순 문서 검색 질문
* 여러 문서를 비교해야 하는 질문
* Tool Calling이 필요한 질문
* Multi-step reasoning이 필요한 질문
* 존재하지 않는 정보를 묻는 질문
* 모호한 질문
* Context를 유지해야 하는 연속 질문

### Metrics

다음 지표를 측정합니다.

* Recall@k
* Faithfulness
* Answer correctness
* Latency
* Token usage
* Estimated cost
* Tool success rate
* Failure rate

평가 결과는 다음 위치에 저장합니다.

```text
evaluation/
└── results/
    ├── metrics.json
    └── report.md
```

---

## 8. Failure Case Analysis

Agent의 실패 사례를 별도로 기록합니다.

주요 분석 대상:

### Hallucination

검색 결과에 없는 정보를 Agent가 생성하는 경우

### Tool Failure

외부 API 또는 Tool 호출에 실패하는 경우

### Context Loss

이전 대화나 실행 상태를 제대로 유지하지 못하는 경우

### Retrieval Failure

관련 문서가 존재하지만 Retriever가 올바른 문서를 찾지 못하는 경우

각 실패 사례에 대해 다음을 기록합니다.

```text
Problem
↓
Reproduction
↓
Root Cause
↓
Fix
↓
Before / After Evaluation
```

---

## 9. Tech Stack

### Backend

* Python
* FastAPI

### LLM / Agent

* LLM API
* LangGraph 또는 자체 Agent workflow

### RAG

* Embedding Model
* Vector Database
* Retriever

### Database

* SQLite / PostgreSQL

### Testing

* pytest
* GitHub Actions

### Evaluation

* Python
* JSON
* pandas / matplotlib

---

## 10. Project Structure

```text
iyuno-agent-portfolio/
│
├── app/
│   ├── agent/
│   ├── rag/
│   ├── tools/
│   ├── services/
│   ├── db/
│   └── api/
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── evaluation/
│
├── evaluation/
├── tests/
├── scripts/
├── docs/
├── demo/
│
├── .github/
│   └── workflows/
│
├── README.md
├── requirements.txt
├── pyproject.toml
└── .env.example
```

---

## 11. Installation

### 1. Clone

```bash
git clone <YOUR_GITHUB_REPOSITORY>
cd iyuno-agent-portfolio
```

### 2. Virtual Environment

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

macOS / Linux:

```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Environment Variables

`.env.example`을 복사하여 `.env`를 생성합니다.

```bash
cp .env.example .env
```

필요한 API Key를 `.env`에 설정합니다.

> 실제 API Key는 GitHub에 업로드하지 않습니다.

---

## 12. Document Ingestion

공개 문서를 수집하고 Vector Store에 저장합니다.

```bash
python scripts/ingest.py
```

실행 결과:

```text
Documents loaded: XX
Chunks created: XXX
Embeddings created: XXX
Vector store updated.
```

---

## 13. Run

FastAPI 서버 실행:

```bash
uvicorn app.main:app --reload
```

API 문서:

```text
/api/docs
```

---

## 14. Example

### Request

```json
{
  "question": "HTTP와 HTTPS의 차이는 무엇인가?"
}
```

### Response

```json
{
  "answer": "...",
  "citations": [
    {
      "document": "example-document.pdf",
      "page": 12
    }
  ],
  "tools_used": [],
  "latency_ms": 1234
}
```

---

## 15. Evaluation

평가 실행:

```bash
python evaluation/evaluate.py
```

예상 결과:

```text
Recall@5       : XX%
Faithfulness   : XX%
Avg Latency    : XXX ms
Tool Success   : XX%
Estimated Cost : $X.XX
```

상세 결과:

```text
evaluation/results/metrics.json
evaluation/results/report.md
```

---

## 16. Testing

전체 테스트:

```bash
pytest
```

특정 테스트:

```bash
pytest tests/test_rag.py
```

CI에서는 모든 테스트가 자동으로 실행됩니다.

---

## 17. Demo

### Demo Video

> TODO: 2분 이내 프로젝트 실행 영상 추가

### Screenshots

```text
demo/
├── screenshots/
└── demo.gif
```

Demo에서 다음 기능을 보여줍니다.

1. 질문 입력
2. Agent Router 실행
3. RAG 검색
4. Tool Calling
5. Multi-step workflow
6. 최종 답변
7. Citation
8. Feedback

---

## 18. Data & License

본 프로젝트는 공개적으로 사용할 수 있는 기술/보안 문서를 대상으로 합니다.

각 데이터셋에 대해 다음 정보를 기록합니다.

| Data       | Source | License | Collected  |
| ---------- | ------ | ------- | ---------- |
| Document 1 | TODO   | TODO    | YYYY-MM-DD |
| Document 2 | TODO   | TODO    | YYYY-MM-DD |

원본 문서의 저작권 및 라이선스를 확인하고 사용 범위를 준수합니다.

---

## 19. Limitations

현재 프로젝트의 한계:

* 실제 기업 내부 데이터는 사용하지 않음
* 실제 Iyuno 내부 Agent 시스템을 재현한 것이 아님
* 공개 문서를 기반으로 구현
* 평가 데이터의 규모가 제한적임
* LLM 응답 특성상 완전한 정답을 보장하지 않음
* 외부 API 장애에 따라 Tool 호출이 실패할 수 있음

---

## 20. Future Improvements

* Multi-agent architecture
* 더 다양한 Tool 추가
* 장기 Context 관리
* 사용자 행동 기반 개선
* A/B Testing
* Retrieval 성능 개선
* Prompt / workflow 자동 평가
* Latency 및 Token Cost 최적화
* Production 환경 monitoring
* Agent reliability 개선

---

## 21. Project Evidence

이 프로젝트에서 구현한 내용을 다음과 같이 확인할 수 있습니다.

| Evidence         | Location                   |
| ---------------- | -------------------------- |
| Agent Workflow   | `app/agent/`               |
| RAG              | `app/rag/`                 |
| Tool Calling     | `app/tools/`               |
| State / Context  | `app/agent/`               |
| Evaluation       | `evaluation/`              |
| Feedback Loop    | `app/services/feedback.py` |
| API / DB         | `app/api/`, `app/db/`      |
| Tests            | `tests/`                   |
| CI               | `.github/workflows/`       |
| Failure Analysis | `docs/failure-cases.md`    |
| Demo             | `demo/`                    |

---

## 22. Job Posting

**Position:** AI Agent Engineer
**Company:** Iyuno
**Location:** Seoul
**Requisition:** JR101122

공고의 핵심 요구사항을 실제 GitHub 프로젝트의 구현 항목과 연결하여 포트폴리오 형태로 구성했습니다.

---

## 23. Author

**Name:** 김동영

**GitHub:** https://github.com/ZERO-NOA/port

**Email:** ehddud022222@gmail.com

---

## 24. Reflection

### What I implemented

* TODO
* TODO
* TODO

### What I learned

* TODO
* TODO
* TODO

### What remains

* TODO
* TODO
* TODO
