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

# 2. php 설치 (7.4 버전 기준)

* 2-1 REMI 리포지트리 추가

    ```
    yum install -y https://rpms.remirepo.net/enterprise/remi-release-7.rpm
    ```

* 2-2 php-fpm 7.4 설치

    ```
    yum install -y php74-php-fpm php74-php-common php74-php-mysqlnd php74-php-mbstring php74-php-pdo php74-php-xml
    ```

# 3. php 설정

* 3-1 www.conf 파일 수정

    ```
    vi /etc/opt/remi/php74/php-fpm.d/www.conf
    ```
    ```
    user = nginx 
    group = nginx
    listen.owner = nginx
    listen.group = nginx
    ```

* 3-2 php.ini 파일 수정 (필요한 경우)

    짧은 태그 설정

    ```
    vi /etc/opt/remi/php74/php.ini
    ```
    ```
    short_open_tag=On
    ```

* 3-3 서비스 시작 및 등록
    ```
    systemctl start php74-php-fpm
    ```
    ```
    systemctl enable php74-php-fpm
    ```

# 4. nginx 설정

    ```
    vi /etc/nginx/conf.d/php.conf
    ```
    ```
    server {
        listen       80;
        server_name  <도메인 이름>;

        charset utf-8;

        root <디렉터리 경로>;
        location / {
            index  index.html index.htm index.php;
        }

        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
    ```

# 5. 추가 설정 (필요시)

* 5-1 SELinux httpd_sys_content_t 설정

    ```
    chcon -R -t httpd_sys_content_t <디렉터리 경로>
    ```

* 5-2 session 폴더 권한 설정 (session 사용 필요시)

    ```
    chown -R root:nginx /var/opt/remi/php74/lib/php/session
    ```

* 5-3 SELinux httpd_can_network_connect_db 권한 설정 (DB 접속 필요시)

    ```
    setsebool -P httpd_can_network_connect_db 1
    ```