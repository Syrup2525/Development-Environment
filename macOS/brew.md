# 기본
* homebrew 업데이트

    ```
    brew update
    ```

* homebrew 로 설치된 모든 패키지 최신버전으로 업데이트

    ```
    brew upgrade
    ```

* 특정 패키지만 최신버전으로 업데이트

    ```
    brew upgrade nginx 
    ```

# 패키지 확인
* 설치된 패키지 확인 

    ```
    brew list
    ```

* 특정 패키지 설치 경로 확인

    ```
    brew list nginx
    ```

# 서비스 관련
* 서비스 확인

    ```
    brew services list
    ```

* 서비스 시작

    ```
    brew services start nginx
    ```

* 서비스 중지 

    ```
    brew services stop nginx
    ```

* 서비스 재시작

    ```
    brew services restart nginx
    ```