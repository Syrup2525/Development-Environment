# 1. JAVA 설치 (필요한 경우)
* 1-1 JAVA 설치
    ```
    yum install java-1.8.0-openjdk
    ```

* 1-2 JAVA 설치 확인
    ```
    java -version
    ```

# 2. Kafka 설치
* 2-1 wget 설치 (필요한 경우) 
    ```
    yum install wget
    ```

* 2-2 kafka 다운
    ```
    wget https://downloads.apache.org/kafka/3.6.0/kafka_2.12-3.6.0.tgz
    ```

* 2-3 압축 해제
    ```
    tar xvf kafka_2.12-3.6.0.tgz
    ```

* 2-4 심볼릭 링크 생성
    ```
    ln -s kafka_2.12-3.6.0 kafka
    ```

# 3. kafka config 파일 수정
* 3-1 IP 확인 (사설 IP 등으로 설정을 원하는 경우, localhost 일때는 필요없음)
    ```
    ifconfig
    ```

* 3-2 ifconfig Command not found 시 net-tools 설치
    ```
    yum install net-tools
    ```

* 3-3 Kafka 클러스터 ID 생성 및 포맷
    ```
    kafka/bin/kafka-storage.sh random-uuid
    ```
    ```
    jaShGO9YR12vntLHlWQQBA 
    ```
    ```
    kafka/bin/kafka-storage.sh format -t jaShGO9YR12vntLHlWQQBA -c kafka/config/kraft/server.properties
    ```
    ```
    vi kafka/config/kraft/server.properties
    ```
    ```
    broker.id=1
    listeners=PLAINTEXT://<IP>:9092
    advertised.listeners=PLAINTEXT://<IP>:9092
    ```

# 4. 테스트
* kafka 실행
    ```
    kafka/bin/kafka-server-start.sh kafka/config/kraft/server.properties
    ```

# 5. 서비스 등록
* 5-1 kafka 스크립트 작성
    ```
    vi /usr/lib/systemd/system/kafka.service
    ```
    ```
    [Unit]
    Description=kafka
    After=network.target

    [Service]
    Type=simple
    User=user
    Group=user
    SyslogIdentifier=kafka
    WorkingDirectory=/home/user/kafka
    Restart=always
    RestartSec=0s
    ExecStart=/home/user/kafka/bin/kafka-server-start.sh /home/user/kafka/config/kraft/server.properties
    ExecStop=/home/user/kafka/bin/kafka-server-stop.sh

    [Install]
    WantedBy=multi-user.target
    ```

* 5-2 kafka 서비스 등록 및 시작
    ```
    systemctl start kafka
    ```
    ```
    systemctl status kafka
    ```
    ```
    systemctl enable kafka
    ```