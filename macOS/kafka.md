# 1. zookeeper 설치
 ```
 brew install zookeeper
 ```

# 2. kafka 설치
 ```
 brew install kafka
 ```

# 3. kafka properties 파일 수정
 ```
 vi /usr/local/etc/kafka/server.properties
 ```

 ```
 listeners=PLAINTEXT://localhost:9092
 advertised.listeners=PLAINTEXT://localhost:9092
 ```

# 4. 실행

* 4-1 zookeeper 실행

    ```
    brew services start zookeeper
    ```

* 4-2 kafka 실행

    ```
    brew services start kafka
    ```

# 5. 실행 확인

 * 5-1 brew 서비스 상태 확인 

    ```
    brew services list
    ```

 * 5-2 kafka 설치 경록 확인

    ```
    brew list kafka
    ```

 * 5-3 kafka bin 폴더로 이동

    ```
    cd /usr/local/Cellar/kafka/3.4.0/bin
    ```

 * 5-4 consumer 실행

    ```
    kafka-console-consumer --bootstrap-server localhost:9092 --topic test-topic --from-beginning
    ```

 * 5-5 producer 실행 (새로운 터미널에서)

    ```
    kafka-console-producer --bootstrap-server localhost:9092 --topic test-topic
    ```

 * 5-6 "5-5 터미널"에서 메시지 전송 후 "5-4 터미널"에서 메시지 출력 확인