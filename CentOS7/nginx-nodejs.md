# 1. nginx 설치

* 1-1 nginx 설치 및 서비스 등록

    ```
    yum install nginx
    ```
    ```
    systemctl start nginx 
    ```
    ```
    systemctl status nginx
    ```
    ```
    systemctl enable nginx
    ```

# 2. node 및 npm 설치

* 2-1 node 설치

    ```
    yum install nodejs
    ```
    ```
    node --version
    ```

* 2-2 npm 설치

    ```
    yum install npm
    ```
    ```
    npm --version
    ```

# 3. npm global module 설치 (sudo 권한 필요)

* 3-1 pm2 설치 (node 프로세스 매니저)

    ```
    npm install -g pm2
    ```

* 3-2 n 설치 (node 버전 관리)

    ```
    npm install -g n
    ```

# 4. node 특정 버전 설치 (sudo 권한 필요)

* 4-1 node 원하는 버전 설치

    * 특정 버전 설치

        ```
        n 16.19.1
        ```

    * lts 설치 

        ```
        n lts
        ```

    * latest 설치

        ```
        n latest
        ```

* 4-2 node 버전 확인

    ```
    node --version
    ```

# 5. node example 파일 작성

* 5-1 폴더 생성

    ```
    mkdir express
    ```
    ```
    cd express
    ```

* 5-2 express 모듈 설치

    ```
    npm i express
    ```

* 5-3 예제 코드 작성

    ```
    vi index.js
    ```
    ``` javascript
    const express = require('express')
    const app = express()
    const port = 3000

    app.get('/', (req, res) => {
        res.send('Hello World!')
    })

    app.listen(port, () => {
        console.log(`Example app listening on port ${port}`)
    })
    ```

* 5-4 express example 실행 확인

    ```
    node index.js
    ```

* 5-5 pm2 서비스 등록

    ```
    pm2 start index.js
    ```

* 5-6 코드 실행 확인

    ```
    curl -X GET localhost:3000
    ```

# 6. nginx config 파일 설정 (root 권한 필요)
* 6-1 nignx proxy 서버 설정

    ```
    vi /etc/nginx/conf.d/node.conf
    ```
    ```
    server {
        listen       80;
        listen       [::]:80;
        server_name  test.example.com;

        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
    ```

* 6-2 IP 직접접근 차단 (필요한 경우)

    ```
    vi /etc/nginx/nginx.conf
    ```
    ```
    ...
    server {
        listen       80 default_server;
	    return       444;
        ...
    }
    ...
    ```

* 6-3 변경사항 적용

    ```
    systemctl restart nginx
    ```

# 7. SELinex 설정변경
    
* 7-1 semanage 명령어 사용을 위한 패키지 설치 (필요한 경우)

    ```
    yum install policycoreutils-python
    ```

* 7-2 현재 허용되어있는 포트 확인

    ```
    semanage port --list | grep http_port_t
    ```

    * http_port_t 항목 확인

        ```
        http_port_t    tcp    80, 81, 443, 488, 8008, 8009, 8443, 9000
        ```

* 7-3 3000 번 포트 허용

    ```
    semanage port --add --type http_port_t --proto tcp 3000
    ```

# 8. 브라우저에서 접속 확인

```
http://test.example.com
```