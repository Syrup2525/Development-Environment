# 1. 설치

* 1-1 패키지 설치
    ```
    yum install certbot
    yum install python-certbot-nginx
    ```

# 2. conf 파일 수정

* 2-1 example.com.conf 파일 생성 및 작성

    ```
    vi /etc/nginx/conf.d/example.com.conf
    ```
    ```
    server {
        listen 80;
        server_name test.blossom.bumblebeecrew.com;

        charset utf-8;

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

# 3. 인증서 발급 및 nginx 자동 구성

* 3-1 인증서 발급

    ```
    certbot --nginx -d example.com
    ```

    본인 이메일 입력

    ```
    Enter email address (used for urgent renewal and security notices)
    ```

    이용 약관 동의

    ```
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Please read the Terms of Service at
    https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
    agree in order to register with the ACME server. Do you agree?
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    (Y)es/(N)o: 
    ```

    뉴스 및 기타 알림 수신 동의
    ```
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Would you be willing, once your first certificate is successfully issued, to
    share your email address with the Electronic Frontier Foundation, a founding
    partner of the Let's Encrypt project and the non-profit organization that
    develops Certbot? We'd like to send you email about our work encrypting the web,
    EFF news, campaigns, and ways to support digital freedom.
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    (Y)es/(N)o:
    ```

* 3-2 인증서 발급 및 적용 완료

    ```
    Account registered.
    Requesting a certificate for example.com
    Performing the following challenges:
    http-01 challenge for example.com
    Waiting for verification...
    Cleaning up challenges
    Deploying Certificate to VirtualHost /etc/nginx/conf.d/example.com.conf
    Redirecting all traffic on port 80 to ssl in /etc/nginx/conf.d/example.com.conf

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Congratulations! You have successfully enabled
    https://example.com
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

    IMPORTANT NOTES:
    - Congratulations! Your certificate and chain have been saved at:
    /etc/letsencrypt/live/example.com/fullchain.pem
    Your key file has been saved at:
    /etc/letsencrypt/live/example.com/privkey.pem
    Your certificate will expire on 2024-01-04. To obtain a new or
    tweaked version of this certificate in the future, simply run
    certbot again with the "certonly" option. To non-interactively
    renew *all* of your certificates, run "certbot renew"
    - If you like Certbot, please consider supporting our work by:

    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
    Donating to EFF:                    https://eff.org/donate-le
    ```

* 3-3 인증서 적용 확인

    ```
    cat /etc/nginx/conf.d/example.com.conf
    ```
    인증서 파일 경로 및 80번 리다이렉트 적용 확인이 가능하다.

# 4. 인증서 갱신 테스트

* 4-1 인증서 갱신 (테스트)

    ```
    certbot renew --dry-run
    ```
    ```
    Saving debug log to /var/log/letsencrypt/letsencrypt.log

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Processing /etc/letsencrypt/renewal/example.com.conf
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Cert not due for renewal, but simulating renewal for dry run
    Plugins selected: Authenticator nginx, Installer nginx
    Starting new HTTPS connection (1): acme-staging-v02.api.letsencrypt.org
    Account registered.
    Simulating renewal of an existing certificate for example.com
    Performing the following challenges:
    http-01 challenge for example.com
    Waiting for verification...
    Cleaning up challenges

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    new certificate deployed with reload of nginx server; fullchain is
    /etc/letsencrypt/live/example.com/fullchain.pem
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    Congratulations, all simulated renewals succeeded: 
    /etc/letsencrypt/live/example.com/fullchain.pem (success)
    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
    ```
