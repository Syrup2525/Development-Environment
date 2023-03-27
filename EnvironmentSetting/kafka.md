# 1. JAVA 설치 (필요한 경우)

* 1-1 JAVA 설치
    ```
    yum install java-1.8.0-openjdk
    ```

* 1-2 JAVA 설치 확인
    ```
    java -version
    ```

# 2. Kafka 다운로드

* 2-1 wget 설치 (필요한 경우) 
    ```
    yum install wget
    ```

* 2-2 kafka 다운
    ```
    wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz
    ```


4. 심볼릭 링크 생성

```
ln -s kafka_2.13-3.4.0.tgz kafka
```


5. IP 확인 (사설 IP 등으로 설정을 원하는 경우)

```
ifconfig
```

(ifconfig Command not found 시 설치)
```
yum install net-tools
```
