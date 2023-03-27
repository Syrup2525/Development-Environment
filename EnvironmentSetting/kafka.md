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
    wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz
    ```

* 2-3 압축 해제
    ```
    tar xvf kafka_2.13-3.4.0.tgz
    ```

* 2-4 심볼릭 링크 생성
    ```
    ln -s kafka_2.13-3.4.0 kafka
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

* 3-3 server.properties 수정
    ```
    vi kafka/config/server.properties
    ```
    ```
    listeners=PLAINTEXT://<IP>:9092
    advertised.listeners=PLAINTEXT://<IP>:9092
    ```

