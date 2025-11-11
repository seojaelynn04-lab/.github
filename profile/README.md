##  Redis 심층 분석 및 활용 프로젝트

### 1. 주제 선정 및 도입 배경 (15점)

* **주제:** "고성능 실시간 데이터 처리를 위한 In-Memory 데이터 스토어: **Redis** 심층 분석"
* **도입 배경 (문제 정의):**
    * 기존 RDBMS(관계형 데이터베이스)의 **성능 한계** (Disk I/O로 인한 속도 저하)
    * 현대 웹 서비스의 **실시간성 요구** (예: 실시간 랭킹, 알림, 세션 관리)
    * 데이터베이스 부하 급증으로 인한 **병목 현상** 발생
* **필요성 (Redis의 해결책):**
    * **In-Memory** 구조를 통한 압도적으로 빠른 데이터 I/O
    * 단순 Key-Value를 넘어선 **다양한 자료구조** (Strings, Lists, Sets, Hashes, Sorted Sets) 제공
    * 캐싱, 세션 스토어, 메시지 큐 등 **다용도(Multi-purpose)** 활용성

### 2. 아키텍처 분석 (25점)

* **핵심 구조:**
    * **Single-Threaded Event Loop (논블로킹 I/O):** Redis가 싱글 스레드임에도 고성능을 내는 이유. (Context Switching 비용 X, 원자성 보장)
    * **In-Memory 데이터 저장:** 모든 데이터를 메모리에 저장하여 빠른 속도 보장
      
* **주요 기술 스택 및 기능:**
    * **자료구조:** 5가지 주요 자료구조(String, List 등)와 각각의 시간 복잡도($O(1)$, $O(\log N)$ 등), 그리고 대표 용도
    * **데이터 영속성 (Persistence):**
        * **RDB (Snapshotting):** 특정 시점의 데이터를 스냅샷으로 저장 (장단점)
        * **AOF (Append Only File):** 모든 쓰기 작업을 로그로 저장 (장단점)
    * **확장성 및 고가용성 (HA):**
        * **Replication (Master-Slave):** 데이터 복제 및 읽기 부하 분산
        * **Sentinel:** 마스터 노드 장애 시 자동 Failover 지원
        * **Cluster:** 데이터 샤딩(Sharding)을 통한 수평적 확장

  **다이어그램:** 클라이언트 - Redis(Sentinel/Cluster) - DB(Persistence) - Replication(Slave) 간의 관계를 보여주는 시스템 아키텍처 다이어그램.

### 3. 활용 사례 및 성과 (25점)

* **주요 활용 사례 (Use Cases):**
    * **1. 캐시 (Cache):** (가장 대표적)
    * **2. 분산락 (Session Store):**
    * **4. 메시지 큐/브로커 (Message Broker):**
        * **사례:** 간단한 채팅 시스템, 실시간 알림 (Pub/Sub 또는 List 자료구조 활용)
* **성과:** (정량적/정성적)
    * "DB 쿼리 80% 감소", "API 응답 속도 200ms -> 20ms로 개선" 등 구체적인 수치 제시 (기술 블로그에서 인용)
    * 서버 확장성 확보, 사용자 경험(UX) 개선

### 4. 유사 사례 비교 및 비판적 시각 (20점)

* **비교 대상:**
    * **Redis vs. Memcached:** (In-Memory 캐시의 양대 산맥)
        * **공통점:** In-Memory, 빠른 속도
        * **차이점:** Redis(다양한 자료구조, 영속성 지원, 싱글 스레드) vs Memcached(단순 String, 영속성 미지원, 멀티 스레드)
    * **Redis vs. RabbitMQ/Kafka:** (메시지 큐 기능 비교)
        * **RabbitMQ/Kafka:** 전문 메시지 브로커 (메시지 보장성, 복잡한 라우팅)
        * **Redis:** 가볍고 빠른 메시징, Pub/Sub. (보장성 약함)
    * **Redis vs. RDBMS (e.g., MySQL):**
        * 데이터 저장소로서의 역할 비교 (속도 vs 일관성/정합성)
* **한계점 및 비판적 시각:**
    * **비용:** In-Memory 특성상 RAM 비용이 비싸다.
    * **데이터 유실 위험:** 영속성(RDB/AOF)을 사용해도 장애 시 RDBMS 대비 데이터 유실 위험이 존재.
    * **싱글 스레드 한계:** 하나의 명령어가 너무 오래 걸리면(e.g., `KEYS *`) 전체 시스템이 멈출 수 있다.

### 5. 프로토타입(PoC) 제작 (20점 - 가산점)

* PoC 주제 1: 캐시 성능을 보여줄 수 있는 것
---

  **자료 출처:**
    [Redis 공식 문서](https://redis.io/) + [Redis github](https://github.com/redis/redis) +기술블로그

