# 1. MongoDB 설치

* 1-1 저장소 추가

    ```
    vi /etc/yum.repos.d/mongodb.repo
    ```
    ```
    [mongodb-org-4.4]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
    ```

* 1-2 MongoDB 설치

    ```
    yum install -y mongodb-org
    ```

* 1-3 서비스 시작 및 부팅 등록
    ```
    systemctl start mongod
    systemctl enable mongod
    ```

# 2. 보안 설정

* 2-1 설정 파일 수정

    ```
    vi /etc/mongod.conf
    ```
    ```
    # network interfaces
    net:
        port: 27017 # 원하는 포트 번호로 변경
        bindIp: 127.0.0.1 # localhost only
    ```
    ```
    systemctl restart mongod
    ```

* 2-2 계정 설정

    ```
    mongo
    ```
    ```
    use admin
    ```
    ```
    db.createUser(
        {
            user: "admin",      # 원하는 사용자 이름으로 변경
            pwd: "password",    # 원하는 비밀번호로 변경
            roles: [ { role: "root", db: "admin" }, "readWriteAnyDatabase" ]
        }
    )
    ```
    ```
    exit
    ```
    ```
    vi /etc/mongod.conf
    ```
    ```
    security:
        authorization: enabled
    ```
    ```
    systemctl restart mongod
    ```

# 3. 추가 설정 사항 (옵션)
* 3-1 다른 경로에 데이터를 저장하는 경우 or 데이터 폴더를 지우고 새로 만든 경우
    ```
    chown -R mongod:mongod /var/lib/mongo
    ```
    ```
    chmod -R 775 /var/lib/mongo
    ```
    ```
    chcon -Rv --type=mongod_var_lib_t /var/lib/mongo
    ```
