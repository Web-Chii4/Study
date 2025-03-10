# Redis란?

> `key - value` 구조의 비정형 데이터를 저장하고 관리하기 위한 오픈소스 기반의 비관계형 NoSQL 데이터베이스 관리 시스템(DBMS)이다.
>

또한, **Redis는 메인 메모리를 활용하는 DB시스템**으로 SSD나 하드디스크에 저장하는 기존의 DB에 비해 빠른 속도를 자랑한다.

이에 따라, Redis는 서버의 캐시, DB, **Message Queue**, Shared Memory 등의 용도로 사용된다.

## Redis의 특징

### 1. 인메모리 DB이다

- Redis는 인메모리 DB로 컴퓨터의 메인 메모리(RAM)에 데이터를 저장한다. 이에 따라, SSD나 하드디스크에 저장하는 기존의 DB시스템에 비해 **데이터의** **접근 속도가 빠르다**.
- RAM을 활용하기 때문에 **휘발성**을 가진다. 하지만, Redis는 이러한 RAM의 휘발성을 방지하기 위해 **AOF(Append On File) 혹은 RDB(Snapshotting) 방식**을 활용해 **데이터의 휘발성을 방지**한다.

### 2. 다양한 Data Type을 지원

Redis는 기본적으로 `Key-Value` 구조로 데이터를 저장한다. key에 Mapping되는 Value의 Data Type은 다양하게 제공된다.
![Image](https://github.com/user-attachments/assets/aabf4a2b-69bf-4c3d-b2e2-8807461d5957)
이 외에도 **Redis Stack**과 **Redis Enterprise**는 `JSON`과 같은 유용한 타입들을 구현하는 확장 모듈을 포함하고 있다.

### 3. 싱글 스레드이다
![Image](https://github.com/user-attachments/assets/05994ea0-75d5-41ed-aa0b-28b2344223ee)
사용자의 Redis명령 입력과 사용자에게 전달하는 출력을 처리하는 스레드는 멀티 스레드로 동작하는 반면, 사용자에게 전달되는 명령어들은 큐에 저장되어 순차적으로 처리된다.

이로 인해 **Redis는 연산의 원자성을 지킬 수 있다**는 장점을 이끌어낸다.

만일, 멀티 스레드로 명령을 처리하게 되면, Race Condition(경쟁 상태)로 인해 데이터의 일관성 및 무결성이 보장되지 못할 수 있다. 애초에 이를 보장하기 위해 싱글 스레드 방식으로 돌면서 데이터의 일관성과 무결성을 보장한다.

사실, Lock기법을 활용해 한번에 하나의 스레드만 데이터에 접근하도록 할 수 있지만, 이는 Lock을 하는데에도 자원이 필요하기 때문에 싱글 스레드가 가장 효율적이다.

### 4. Redis 파티셔닝

DB의 파티셔닝과 같이 데이터의 크기가 커지면, 탐색의 용이를 위해 파티셔닝 기능을 제공한다. Redis에서 파티셔닝 기능을 제공함으로써 이루고자 하는 목표는 다음과 같다.

1. 다수의 컴퓨터들의 메모리를 합해 큰 DB를 만들 수 있게 한다.(RAM을 활용하기 때문)
2. 컴퓨팅 파워, 다수의 컴퓨터, 다수의 컴퓨터의 네트워크 대역폭, 네트워크 어댑터들을 확장할 수 있게 한다.

파티셔닝 기능을 제공하기 위해 `Sentinel`과 `Cluster` 방식을 제공한다.

## 특징에 따른 구동 방식 소개

### 1. AOF와 RDB

휘발성을 지닌 RAM에 저장하는 Redis는 데이터의 **영속성을** 보장하기 위해 AOF와 RDB방식을 활용해 영속성을 보장한다.

**영속성(=지속성)이란?**

> 데이터를 생성한 **프로그램의 실행이 종료되더라도 사라지지 않는 데이터의 특성**을 말한다.
>

**AOF(Append On File) 방식**

> 명령이 실행될 때마다 명령을 **파일에 기록**해 데이터의 손실이 거의 없다.
>

Redis의 모든 **write/update연산**을 log파일에 기록하는 형태이다.

**RDB(Snapshotting) 방식**

> 특정 시점의 메모리에 있는 **모든 데이터를 바이너리 파일로 저장**한다.
>

해당 방식은 **스냅샷(특정 시점의 데이터를 찍어 저장한다) 방식**으로 동작하기 때문에 **Git과 같이** **버저닝이 가능**하다.

또한, 저장하는 **특정 시점은 개발자가 직접 설정**할 수 있다.

### 2. Redis 파티셔닝

Redis에서는 다수의 컴퓨터를 활용해 저장 공간을 확장하거나 처리율을 높이기 위해 파티셔닝 기능을 제공한다. 해당 기능을 제공하기 위한 방식에는 `Sentinel` 방식과 `Cluster` 방식이 있다.

![Image](https://github.com/user-attachments/assets/73954cac-4162-49a1-aff0-b43342d3087d)

위의 사례를 예시로 들어보자.

- Node1 : `userID`가 1 ~ 1000까지 담당하는 Redis인스턴스이다.
- Node2 : `userID`가 1001 ~ 2000까지 담당하는 Redis인스턴스이다.
- Node3 : `userID`가 2001 ~ 3000까지 담당하는 Redis인스턴스이다.

**Redis파티셔닝이 동작**

1. **Data Distribution(데이터 분산)**

   위의 예시에 따르면, Redis의 각 노드는 `userID` 의 수에 따라 담당하는 데이터들은 달라진다. 만약, `userID`가 **1700**인 데이터가 저장된다면, **Node2**에 저장될 것이다.

2. **Data Retrieval(데이터 반환)**

   만약, 특정 `userID` 를 활용해 데이터를 반환받고자 한다면, 해당 데이터 요청 명령은 명시한 `userID` 값에 따라 처리하는 노드는 달라진다. 예로, `userID` 가 1700인 데이터를 요청한다면, Node2에 쿼리가 전달되고, 데이터가 반환된다.

3. **Load Balancing(부하 분산)**

   Redis에 전달되는 요청들은 **cluster**전반에 걸쳐 트래픽을 분산시켜준다. 해당 기능은 하나의 노드가 hotspot이 되는 것으로부터 막아준다.

4. **Scaling(규모 확장)**

   Redis에 저장되는 데이터와 traffic이 증가되는 경우 더 많은 Redis node들을 cluster에 추가할 수 있고, 데이터를 재분배 할 수 있다. 예시로, 위에는 3개의 노드들만 존재하는데 3001 ~ 4000까지 `userID`를 저장하기 위해 Node4를 추가할 수 있다.

5. **Failover(시스템 대체 작동)**

   만약, Redis노드 중 하나가 다운된 경우, 처리할 수 있는 방법이다. 데이터 가용성을 보장하기 위해 노드를 복제하거나, 슬레이브 노드를 새로운 기본 노드로 승격하는 처리를 담당한다.


**Redis 파티셔닝의 Type**

1. **Range-Based Partitioning(범위 기반 파티셔닝)**

   개발자가 정의한 범위를 각각의 Redis노드에 할당한다. 개발자가 해당 데이터가 어느 데이터셋에 저장되는지 예측할 수 있는데 유용하다.

2. **Hash-Based Partitioning(해시 기반 파티셔닝)**

   각각의 Data key에 Hash함수를 적용해 어느 Redis Node에 저장할 것인지 결정짓는다. Node에 걸쳐 key의 분산을 제공하지만, 노드들이 추가되거나 제거되었을 때 재분배가 필요하다.

3. **Consistent Hashing(일관된 해싱)**

   분산 시스템들 중 가장 인기있는 방법이다. 새로운 노드가 추가되거나 삭제되는 경우 데이터의 이동을 최소화한다. → 해시링(안정해시)


**`Sentinel`과 `Cluster`방식(파티셔닝)**

**Sentinel 방식**
![Image](https://github.com/user-attachments/assets/416ef48f-eaf0-40e2-bbc8-bb25f43c6228)
> Master - Slave 구조를 통해 **고가용성을** 보장하기 위해 사용된다.
>

즉, Redis인스턴스를 1개 사용하는 경우 해당 인스턴스가 종료됐을 경우 대체할 수 있는 인스턴스를 자동으로 선정해주는 방식이다.

**구성요소)**

**Master**

- 데이터를 기록하는 주요 서버이다.

**Slave**

- 마스터 인스턴스의 데이터를 복제하고있다.
- Slave는 Master의 오류로 인해 다운되면 빠르게 대체할 수 있도록 지속적으로 Master의 데이터를 복제한다.
- Read작업 처리도 가능

**Sentinel**

- Master와 Slave의 상태를 모니터링하고, Master에 장애가 발생하면 Slave중 하나를 Master로 올려 자동 복구 기능을 수행한다.
- Client가 Master인스턴스의 주소를 알 수 있도록 반환해준다.

**Cluster 방식**
![Image](https://github.com/user-attachments/assets/6ccae72f-e5ff-4cce-8e38-5ee8be94f9bf)
> 샤딩을 통해 데이터를 분산 저장 및 처리할 수 있는 방식을 지원해 **고가용성과 확장성**을 제공하는 기능이다
>

해당 방식은 센티넬(Sentinel)방식의 고가용성에 더해 샤딩(여러 DB에 나눠진 테이블을 저장) 기능을 지원하기 때문에 **확장성까지** 제공한다.

**동작 방식)**

- **여러 개의 마스터 노드가 존재**하고, 각 마스터는 **특정 해시 슬롯을 담당**한다.
- 각각의 마스터 노드에는 하나 이상의 슬레이브 노드가 존재한다. (고가용성 보장)
- 마스터 노드에 장애가 발생하면, **해당 마스터를 담당하던 슬레이브 노드 중 하나가 자동으로 마스터로 승격**된다. (Sentinel 없이도 장애 감지 및 복구하기 위해 클러스터 내 다른 노드들이 이를 감지하고, 새로운 클러스터 구성을 클라이언트에게 전달한다)
- **클라이언트는 Key의 해시 슬롯을 계산해 적절한 노드에 직접 요청을 보낸다**. 이를 위해 초기 연결 시 클러스터(마스터 or 슬레이브)로 부터 전체 슬롯 정보를 얻어 캐싱한다.

**해시 슬롯이란?**

> 각각의 **Redis마스터 노드가 담당하는 해시 값의 범위**이다
>

총 16384개의 해시 슬롯으로 나뉘며, 각 Redis노드는 특정 해시 슬롯의 범위를 담당한다.

### 참

[레디스](https://ko.wikipedia.org/wiki/%EB%A0%88%EB%94%94%EC%8A%A4)

[Understand Redis data types](https://redis.io/docs/latest/develop/data-types/)

[Redis persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)

[Partitioning - Redis Documentation](https://redis-doc-test.readthedocs.io/en/latest/topics/partitioning/)

[Redis란 무엇일까? - Redis의 특징과 사용 시 주의점](https://velog.io/@wnguswn7/Redis%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C-Redis%EC%9D%98-%ED%8A%B9%EC%A7%95%EA%B3%BC-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EC%A3%BC%EC%9D%98%EC%A0%90)

[[REDIS] 📚 캐시 데이터 영구 저장하는 방법 (RDB / AOF)](https://inpa.tistory.com/entry/REDIS-%F0%9F%93%9A-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%98%81%EA%B5%AC-%EC%A0%80%EC%9E%A5%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%9D%98-%EC%98%81%EC%86%8D%EC%84%B1)

[Understanding Redis Partitioning - GeeksforGeeks](https://www.geeksforgeeks.org/understanding-redis-partitioning/#understanding-redis-partitioning)

[Redis Explained](https://architecturenotes.co/p/redis)