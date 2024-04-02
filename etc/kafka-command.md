# kafka-topics
### 토픽 목록 조회
```
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

<br>

# kafka-console-consumer
### 메시지 소비 (consume)
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic <topic_name>
```

<br>

# kafka-console-producer
### 메시지 생성 (producer)
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic <topic_name>
```

<br>

# kafka-consumer-groups
### 컨슈머 그룹 현재 상태 조회

```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group <consumer_group_id> --describe 
```
* `GROUP` consumer 그룹의 ID
* `TOPIC` 해당 consumer 그룹이 구독중인 토픽의 이름 
* `PARTITION` 토픽 내에서 해당 파티션의 숫자
* `CURRENT-OFFSET` 현재 consumer 그룹의 오프셋 커밋 위치
* `LOG-END-OFFSET` 로그 끝 오프셋, 가장 최근에 기록된 메시지의 오프셋 위치
* `LAG` 처리되지 않은 메시지 개수 (`CURRENT-OFFSET` 와 `LOG-END-OFFSET` 의 차이)
* `CONSUMER-ID` consumer 고유 식별자
* `HOST` consumer 가 실행중인 호스트의 IP 주소
* `CLIENT-ID` consumer 클라이언트 식별자
