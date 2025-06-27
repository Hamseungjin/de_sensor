
---

## 🔧 **전체 시스템 워크플로우 요약**

```mermaid
graph LR
    A["📱 스마트폰<br/>Sensor Logger"] -->|"HTTP POST<br/>센서 데이터"| B["🚀 Producer<br/>FastAPI<br/>:8000"]
    B -->|"JSON 메시지"| C["📨 Kafka<br/>Message Broker<br/>:9092"]
    C -->|"메시지 소비"| D["🔄 DB Consumer<br/>Python Script"]
    D -->|"CSV Upload"| E["🗄️ QuestDB<br/>Time Series DB<br/>:8812"]
    E -->|"SQL Query"| F["🖥️ UI Server<br/>FastAPI<br/>:5000"]
    F -->|"Server-Sent Events"| G["📊 Web Dashboard<br/>Chart.js"]

    H["⚙️ Zookeeper<br/>:2181"] -.->|"좌표"| C
    I["🔍 Kafka UI<br/>:18080"] -.->|"모니터링"| C
```

---

## 🕒 **실시간 데이터 플로우 (SequenceDiagram)**

```mermaid
sequenceDiagram
    participant Phone as 📱 스마트폰
    participant Producer as 🚀 Producer
    participant Kafka as 📨 Kafka
    participant Consumer as 🔄 DB Consumer
    participant QuestDB as 🗄️ QuestDB
    participant UI as 🖥️ UI Server
    participant Browser as 📊 브라우저

    Phone->>Producer: POST /phone-producer/<br/>센서 데이터 전송
    Producer->>Kafka: 메시지 발행<br/>topic: sensor_data
    Producer->>Phone: 200 OK 응답

    Note over Consumer: 실시간 메시지 소비
    Kafka->>Consumer: 메시지 소비
    Consumer->>QuestDB: CSV 형태로<br/>데이터 저장

    Note over UI: 1초마다 폴링
    UI->>QuestDB: SQL 쿼리로<br/>최근 데이터 조회
    QuestDB->>UI: 센서 데이터 반환
    UI->>Browser: Server-Sent Events<br/>실시간 전송
    Browser->>Browser: Chart.js로<br/>실시간 차트 업데이트
```

---

## 📌 **시스템 구성 요약**

| 구성 요소                       | 설명                           | 포트       |
| --------------------------- | ---------------------------- | -------- |
| **📱 스마트폰**                 | 3축 센서 데이터 전송                 | -        |
| **🚀 Producer (FastAPI)**   | HTTP POST로 데이터 수신 후 Kafka 전송 | `:8000`  |
| **📨 Kafka**                | 메시지 브로커 (sensor\_data 토픽)    | `:9092`  |
| **⚙️ Zookeeper**            | Kafka 클러스터 좌표 저장             | `:2181`  |
| **🔍 Kafka UI**             | Kafka 메시지 모니터링               | `:18080` |
| **🔄 DB Consumer**          | Kafka 메시지를 수신 후 CSV 업로드      | -        |
| **🗄️ QuestDB**             | 시계열 DB (센서 데이터 저장)           | `:8812`  |
| **🖥️ UI Server (FastAPI)** | 실시간 데이터 쿼리 및 SSE 전송          | `:5000`  |
| **📊 Web Dashboard**        | Chart.js로 실시간 데이터 시각화        | -        |

---

## ⚙️ **Docker 명령어 정리**

| 작업             | 명령어                         |
| -------------- | --------------------------- |
| 시스템 전체 실행 및 빌드 | `docker-compose up --build` |
| 전체 종료          | `docker-compose down`       |
| 컨테이너 상태 확인     | `docker-compose ps`         |

---

## 🔄 **기존 3축 센서 → 다른 센서로 확장 시 고려사항**

* 📊 **데이터베이스 스키마 변경**: QuestDB 테이블 컬럼 추가/변경 필요
* 🎨 **UI 차트 로직 수정**: Chart.js 구성 변경
* 🔄 **Consumer 처리 로직 수정**: 메시지 파싱 및 CSV 포맷 변경
* 📡 **API 스키마 수정**: `/phone-producer`의 request body 구조 수정

---

