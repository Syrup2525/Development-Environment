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
