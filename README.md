# 🎯 Vibecode: RAG 기반 Java 리팩토링 자동화 봇

> **"바이브 코딩의 속도에 실무 개발자의 견고함을 더하다"**
>
> AI 코딩 도구가 놓치기 쉬운 컨벤션, 예외 처리, 유지보수성을 n8n 기반 RAG 파이프라인으로 자동 검수합니다.

---

## 📌 프로젝트 개요
Vibecode는 n8n을 오케스트레이터로 활용하여, Slack 멘션 한 번으로 **최신 Java 스타일 가이드(Google, Naver 등)를 실시간 참조(RAG)**하고, 단순 동작을 넘어선 '엔지니어링 수준의 리팩토링' 제안을 제공합니다.

---

## 👥 팀원 소개
| <img src="https://github.com/seajihey.png" width="200px"> | <img src="https://github.com/jongyeon0214.png" width="200px"> | <img src="https://github.com/minykang.png" width="200px"> |
| :---: | :---: | :---: |
| [서지혜](https://github.com/seajihey) | [김종연](https://github.com/jongyeon0214) | [강민영](https://github.com/minykang) |

---

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

![alt text](image.png)

---

## 🎯 프로젝트 목표
1. ai를 통해 작성된 코드를 자동화된 리뷰로 실무 코드와 가까운 품질로 끌어올립니다.
2. n8n을 활용하여 Slack/Webhook 등으로 들어오는 입력을 트리거로, “수집 → 정제 → 인덱싱(RAG) → 검색 → 응답”을 노드 기반으로 오케스트레이션 하며 실무형 자동화 파이프라인을 직접 구축합니다.
3. Google Style Guide/기업 컨벤션(네이버)/기술 블로그 등 권위 있는 문서 근거를 리뷰와 함께 제시해 “왜 이렇게 고쳤는지”를 설득력 있게 제공합니다.


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

