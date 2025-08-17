## 실습으로 배우는 선착순 이벤트 시스템
> 인프런에 [실습으로 배우는 선착순 이벤트 시스템](https://www.inflearn.com/course/%EC%84%A0%EC%B0%A9%EC%88%9C-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%8B%A4%EC%8A%B5/dashboard) 강의를 수강하며 작성한 예제 코드
 
#### 요구사항 정의

선착순 100명에게 할인쿠폰을 제공하는 이벤트를 진행하고자 한다.

- 이 이벤트는 아래와 같은 조건을 만족하여야 한다.
    - 선착순 100명에게만 지급되어야 한다.
    - 101개 이상이 지급되면 안된다.
    - 순간적으로 몰리는 트래픽을 버틸 수 있어야한다.


##### 레디스를 통한 작업환경 세팅

- 레디스 다운로드
```
docker pull redis
```

- 레디스 실행
```
 docker run --name myredis -d -p 6379:6379 redis 
```

- 레디스 gradle 종속성
```
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

#### kafka 설치

카프카를 설치하기 위한 docker-compose.yml

```
version: '2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
  kafka:
    image: wurstmeister/kafka:2.12-2.5.0
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 127.0.0.1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

카프카 실행
```
docker-compose up -d
```

중지
```
docker-compose down
```

#### 카프카 토픽 생성, 프로듀서 실행, 컨슈머 실행 명령


토픽생성
```
docker exec -it kafka kafka-topics.sh --bootstrap-server localhost:9092 --create --topic testTopic
```
토픽은 한번 생성하고 나면 already exists 라고 뜬다

프로듀서 실행
```
docker exec -it kafka kafka-console-producer.sh --topic testTopic --broker-list 0.0.0.0:9092
```

컨슈머 실행
```
docker exec -it kafka kafka-console-consumer.sh --topic testTopic --bootstrap-server localhost:9092
```
kafka gradle 종속성
```
implementation 'org.springframework.kafka:spring-kafka'
```




