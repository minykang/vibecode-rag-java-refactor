# 🎯 Vibecode: RAG 기반 Java 리팩토링 자동화 봇

> 💬 **"바이브 코딩의 속도에 실무 개발자의 견고함을 더하다"**
>
> ✨ AI 코딩 도구가 놓치기 쉬운 컨벤션, 예외 처리, 유지보수성을 **n8n 기반 RAG 파이프라인**으로 자동 검수합니다.


---

## 👥 팀원 소개
| <img src="https://github.com/seajihey.png" width="200px"> | <img src="https://github.com/jongyeon0214.png" width="200px"> | <img src="https://github.com/minykang.png" width="200px"> |
| :---: | :---: | :---: |
| [서지혜](https://github.com/seajihey) | [김종연](https://github.com/jongyeon0214) | [강민영](https://github.com/minykang) |

---



## 📌 프로젝트 개요
Vibecode는 n8n을 오케스트레이터로 활용하여 Slack 멘션 한 번으로 최신 Java 스타일 가이드(Google, Naver 등)를 실시간 참조(RAG)하고 단순 동작을 넘어선 엔지니어링 수준의 리팩토링 제안을 제공합니다.

<br>

---

## 🎖️ 주요 기능

* **실시간 RAG 기반 코드 검수**
    * **공신력 있는 근거**: Google 및 NAVER Java 스타일 가이드를 실시간 수집 및 정제하여 분석에 활용합니다.
    * **지능형 데이터 적재**: Gemini-004 임베딩과 Pinecone 벡터 DB를 통해 단순 생성이 아닌 규정 준수 여부를 판단합니다.



* **근거 기반 리팩토링**
    * **신뢰할 수 있는 답변**: 모든 수정 제안에 [E1], [E2]와 같은 **Evidence ID**를 매핑하여 출처를 명확히 제시합니다.
    

* **성능 최적화 게이트** 💡
    * **비용 및 지연 절감**: JavaScript Code 노드로 다수의 인덱싱 신호를 단일 신호로 축약하여 LLM 중복 호출을 원천 차단합니다.
    * **안정적 오케스트레이션**: 데이터 적재가 완료된 시점에만 에이전트가 실행되도록 설계하여 워크플로우의 안정성을 확보했습니다.


* **대화형 협업 UI**
    * **스마트 트리거**: 슬랙 봇 멘션 시에만 즉각 반응하며, 분석 결과를 **스레드(Thread)** 답장으로 제공해 가독성을 유지합니다.
    * **워크플로우 통합**: n8n을 중심으로 수집-인덱싱-추론-응답 전 과정을 자동화 파이프라인으로 구축했습니다.
---

<br>


## 🧰 기술 스택

| 분류 | 기술 |
| :--- | :--- |
| **Orchestration** | **n8n** (Workflow Automation) |
| **LLM / Embedding** | **Google Gemini 2.5 Flash**, Gemini Text-Embedding-004 |
| **Vector DB** | **Pinecone** (Serverless, Cosine Similarity) |
| **Communication** | **Slack API** (Trigger & Response) |
| **Connectivity** | **ngrok** (로컬-클라우드 협업 터널링) |


---

<br>

## 🎯 프로젝트 목표

**"AI의 생산성에 시니어 엔지니어의 전문성을 결합한 자동화 파이프라인 구축"**


* **🔗 n8n 기반의 End-to-End Workflow**
    * [수집 → 정제 → 인덱싱(RAG) → 검색 → 응답] 전 과정을 n8n 노드로 직접 설계하여 실무형 자동화 파이프라인 구축 역량을 확보합니다.



* **📚 공신력 있는 근거 중심의 리팩토링**
    * Google Style Guide, NAVER Hackday 등 권위 있는 문서 데이터를 실시간 참조하여, 리팩토링 제안에 대한 객관적인 근거와 설득력을 제공합니다.
---

<br>


## ⚙️ 시스템 아키텍쳐

<img src="image.png" alt="시스템 아키텍쳐" width="1000">

---

## n8n 워크플로우
<img src="image-1.png" alt="워크플로우" width="1000">

## 🚀 Workflow Steps

1. **🟣 Slack Trigger**: 슬랙 @멘션을 수신하여 리팩토링 워크플로우를 시작합니다.
2. **🌐 HTTP Request**: 구글·네이버의 최신 자바 스타일 가이드 원문을 실시간 수집합니다.
3. **🧽 HTML Extract**: 수집된 HTML 문서에서 노이즈를 제거하고 본문 텍스트만 추출합니다.
4. **🧩 Schema Normalize**: 추출된 텍스트에 출처(URL) 메타데이터를 부여하고 규격을 통일합니다.
5. **🔀 Data Merge**: 분기되어 처리된 여러 문서 데이터를 하나의 흐름으로 통합합니다.
6. **📚 RAG Indexing**: 데이터를 청크로 분할 후 Gemini 임베딩을 생성하여 Pinecone에 저장합니다.
7. **🛑 Sync Gate** 💡: 인덱싱 완료 시까지 대기 후 단일 신호를 발생시켜 AI의 중복 실행을 방지합니다.
8. **🤖 AI Agent**: Pinecone 검색 결과를 근거로 Gemini가 리팩토링 답변을 생성합니다.
9. **📩 Slack Delivery**: 최종 리뷰 결과를 슬랙 스레드 답장 형식으로 전송합니다.

---

<br>

## 🚀 기술적 차별점 (Technical Highlight)

### 💡 동기화 게이트 (Synchronization Gate)
* **문제 상황**: 문서 인덱싱 후 생성된 수십 개의 데이터 청크가 에이전트에 동시에 전달되어 LLM이 불필요하게 반복 실행되는 비용/성능 이슈 발생.
* **해결 방법**: **JavaScript Code 노드**를 활용해 인덱싱 완료 시 단일 `ready: true` 신호만 반환하도록 설계.
* **결과**: **Merge(Choose Branch)** 노드를 통해 Slack 질문 원본과 완료 신호가 모두 도착했을 때만 에이전트가 실행되도록 제어하여 효율성을 극대화했습니다.

---

<br>

## 📈 실행 결과

* **Input**: Java 코드와 함께 리팩토링 요청 (@Vibecode)
* **Process**: 컨벤션 실시간 수집 → Pinecone 인덱싱 → Gemini RAG 분석 → 근거 기반 답변 생성
* **Output**: 
    - 리팩토링된 최적화 코드 제공
    - **Naver Hackday Java Coding Convention** 등 공신력 있는 근거 문서(References) 인용

---

**Vibecode**는 단순히 코드를 고치는 것을 넘어, 개발자가 "왜 이렇게 고쳐야 하는지"를 학습할 수 있는 환경을 제공합니다.



## 🚨 트러블슈팅

1. **토큰이슈** - 실제사용하기엔 토큰개수 너무적어 workflow를 구성하는데 어려움이 있었습니다.
* **Solution**: 무료요금제로 토큰을 확보하였습니다

2. **협업환경** - 셀프호스팅을 통해 협업을 하려했지만 속도가 너무 느려 진행하는데 어려움이 있었습니다.
* **Solution**: ngrok링크를 통하여 초반에는 ngrok으로 연결해서 진행하였고, 후에는 클라우드환경을 이용해 협업을 진행하였습니다. 

3. **workflow구성** - 워크파인콘 하나만 썼을때 제미나이랑 연결이 안되는 이슈가 발생했습니다.
* **Solution**: 파인콘을 두개로 구성하여 하나는 insert document, retrieve 로 역할을 나누어 연결하였습니다.

4. **성능시각화** - n8n에서 발생한 Pinecone 실행 로그 및 검색 결과 데이터를 Elasticsearch/Kibana로 전송하여 시각화 시도 중, 대용량 로그 데이터 및 복잡한 벡터 데이터 처리 과정에서 시스템 부하 발생 및 전송 실패하였습니다.
    * **⭐시각화의 원래 목적 (Original Goal)**
        * **4-1. RAG 답변 품질 검증:** 검색 결과의 Similarity Score를 시각화하여 AI 답변의 신뢰도 측정.

        * **4-2. 병목 구간 탐지:** 워크플로우 내 Pinecone 노드의 **처리 속도**를 모니터링하여 실시간 성능 분석.
    * **분석 (Cause):** n8n에서 쏟아지는 방대한 실행 메타데이터와 고차원 벡터 정보를 실시간으로 ELK 스택(Elasticsearch, Logstash, Kibana)이 수용하기에는 데이터 처리량(Throughput) 한계 및 리소스 충돌 발생하였습니다.
    * **Solution:** 추후에 대규모 인프라 모니터링에 특화된 Grafana 툴과 전용 시각화 솔루션을 활용하여 발전시키려 합니다.






## 🔭 향후 발전 가능성

