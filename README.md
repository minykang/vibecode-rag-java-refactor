# 🎯 Vibecode: RAG 기반 Java 리팩토링 자동화 봇

> **"바이브 코딩의 속도에 실무 개발자의 견고함을 더하다"**
>
> AI 코딩 도구가 놓치기 쉬운 컨벤션, 예외 처리, 유지보수성을 n8n 기반 RAG 파이프라인으로 자동 검수합니다.

---

## 📌 프로젝트 개요
Vibecode는 n8n을 오케스트레이터로 활용하여, Slack 멘션 한 번으로 **최신 Java 스타일 가이드(Google, Naver 등)를 실시간 참조(RAG)**하고, 단순 동작을 넘어선 '엔지니어링 수준의 리팩토링' 제안을 제공합니다.

---

## 🧰 기술 스택

| 분류 | 기술 |
| :--- | :--- |
| **Orchestration** | **n8n** (Workflow Automation) |
| **LLM / Embedding** | **Google Gemini 1.5 Flash**, Gemini Text-Embedding-004 |
| **Vector DB** | **Pinecone** (Serverless, Cosine Similarity) |
| **Communication** | **Slack API** (Trigger & Response) |
| **Connectivity** | **ngrok** (로컬-클라우드 협업 터널링) |

---

## 🚀 기술적 차별점 (Technical Highlight)

### 💡 동기화 게이트 (Synchronization Gate)
* **문제 상황**: 문서 인덱싱 후 생성된 수십 개의 데이터 청크가 에이전트에 동시에 전달되어 LLM이 불필요하게 반복 실행되는 비용/성능 이슈 발생.
* **해결 방법**: **JavaScript Code 노드**를 활용해 인덱싱 완료 시 단일 `ready: true` 신호만 반환하도록 설계.
* **결과**: **Merge(Choose Branch)** 노드를 통해 Slack 질문 원본과 완료 신호가 모두 도착했을 때만 에이전트가 실행되도록 제어하여 효율성을 극대화했습니다.

---

## 📈 실행 결과

* **Input**: Java 코드와 함께 리팩토링 요청 (@Vibecode)
* **Process**: 컨벤션 실시간 수집 → Pinecone 인덱싱 → Gemini RAG 분석 → 근거 기반 답변 생성
* **Output**: 
    - 리팩토링된 최적화 코드 제공
    - **Naver Hackday Java Coding Convention** 등 공신력 있는 근거 문서(References) 인용

---

**Vibecode**는 단순히 코드를 고치는 것을 넘어, 개발자가 "왜 이렇게 고쳐야 하는지"를 학습할 수 있는 환경을 제공합니다.