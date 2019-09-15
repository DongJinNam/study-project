# Kafka

[https://kafka.apache.org/intro](https://kafka.apache.org/intro)

### 등장배경
* 기존 플랫폼 시스템이 복잡해지고 파이프라인이 파편화됨에 따라 개발이 지연되고 데이터의 신뢰성이 떨어짐
* 시스템 전체에서 데이터 전송을 신뢰 있고 효율적인 관리를 위해서 중앙에서 관리함.
  * Producer, Consumer 분리
  * 메시징 시스템 처럼 영구 메시지 데이터를 여러 컨슈머에게 ㅎ용
  * 높은 처리량 목적
  * 데이터 증가에 따른 Scale out 방식
  * Multi Producer / Multi Consumer 도입

### Use cases
* Messaging System
* Website Activity check and monitor
* Log Aggregation(로그 통합)
* Stream Processing(스트리밍 처리) and Batch Processing(일괄 처리)

### 특징
* Pub/Sub 개념, 메시지 큐와 기업용 메시징 시스템과 유사
* fault-tolerance 방식으로 데이터 보관
* 플랫폼 내 서버 간 실시간 데이터 전송 파이프라인 개념
* 고성능이며, TCP Protocol 사용

### Few Concepts
* 멀티 데이터센터로 확장 가능한 서버 형태 클러스터로 구축됨
* 스트림 형태의 데이터를 ***topic***으로 부름.
* 모든 데이터는 key, value, timestamp 형태로 저장됨.

### core APIs
* Producer API
  * kafka topics에 데이터 publish를 허용하는 api
* Consumer API
  * topic에 있는 데이터들을 subscripte를 허용하는 api
* Streams API
  * stream processor로의 어플리케이션 역할을 허용. 효율적인 데이터 transfer를 담당
* Connector API
  * 기존 어플리케이션이나 데이터 시스템에 kafka topics 연결을 허용하는 api
    
    
### Topics and Logs
* 하나의 토픽 당 여러 partition 가질 수 있음
  * producer는 topic에 데이터를 기록할 수 있으며, 이는 각 partition에 순차적으로 저장이 된다.
  * 각 partition은 정렬되어 있고, 순서 변경이 불간으한 record들의 연속된 집합
  * 각 partition에 할당되는 레코드들은 sequential id를 가짐(=offset)
  * offset은 consumer에 의해 제어된다.
  
### Distribution
* todo 

### Producers
* topic 내 어떤 파티션에 레코드를 저장할지에 대한 권한을 가짐.

### Consumers
* todo
