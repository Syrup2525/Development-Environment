# 1. SSL 인증서 설치 (GOGET SSL 기준)

* 1-1 메인 > SSL Certificate Details > 도메인 선택 > All files

* 1-2 인증서 합치기
    ```
    cat domain.crt CA.crt Certification_Authority.crt > crt
    ```

* 1-3 crt 수정
    ```
    vi crt
    ```
    해당 부분을
    ```
    -----END CERTIFICATE----------BEGIN CERTIFICATE-----
    ```
    아래처럼 줄바꿈 처리한다.
    ```
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    ```

* 1-4 인증서 파일 경로 수정 (필요한 경우)
    ```
    mkdir /etc/nginx/ssl
    ```
    ```
    chcon -Rv --type=httpd_config_t /etc/nginx/ssl
    ```
    ```
    mv crt /etc/nginx/ssl
    ```

* 1-5 개인 키 파일 생성
    ```
    vi /etc/nginx/ssl/key
    ```

# 2. conf 파일 수정

* 1-1 conf 파일에 다음과 같이 ssl 적용 (예시)

    ```
    server {
        listen 80;
        server_name example.com;
        root html;
	
        location / {
            return 301 https://example.com$request_uri;
        }
    }

    server {
        listen      443 ssl;
        server_name example.com;

        charset utf-8;

        # ssl 인증서 추가
        ssl_certificate     /etc/nginx/ssl/crt;
        ssl_certificate_key /etc/nginx/ssl/key;

        # root
        location / {
            root /home/user/public-html/example/;
            index index.html index.htm index.php;

            # php 
            location ~ \.php$ {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include        fastcgi_params;
            }
        }

        # /example (node listen 3000)
        location /example {
            proxy_pass http://127.0.0.1:3000;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # /socket (node listen 3001 webSocket)
        location /socket {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $host;

            proxy_pass http://127.0.0.1:3001;

            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            roxy_set_header Connection "upgrade";
        }

        # /example/php
        location ^~ /example/php {
            alias /home/user/php/example/;
            index index.html index.htm index.php;

            location ~ \.php$ {
                fastcgi_pass   127.0.0.1:9000;
                include        fastcgi_params;
                fastcgi_param  SCRIPT_FILENAME $request_filename;
            }
        }
    }
    ```

# 3. nginx 데몬 재시작 및 SSL 확인

* 3-1 nginx conf 파일 확인 및 데몬 재시작

    ```
    nginx -t
    ```
    ```
    systemctl restart nginx
    ```
    ```
    systemctl status nginx
    ```

* 3-2 SSL 확인
    
    [여기](https://www.sslshopper.com/ssl-checker.html) 또는 [여기](https://decoder.link/sslchecker/) 에 접속해서 본인 도메인을 넣고 적용이 잘 되었는지 확인 (모든 항목이 성공 표시가 되어야함)