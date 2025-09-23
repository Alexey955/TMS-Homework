## Задание 1
1) Создал файл для веб-страницы /var/www/lesson_16/task_01/index.html:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Студент: Алексей Васильев</p>
       <p>Тема занятия: SSL/TLS</p>
   </body>
   </html>
   ```

2) Сгенерировал сертификат lesson_16_task_01.crt и приватный ключ lesson_16_task_01.key:

   ```bash
   openssl req -newkey rsa:4096 -nodes -sha256 -keyout ./lesson_16_task_01.key -x509 -addext "subjectAltName = IP:10.129.0.34" -days 365 -out ./lesson_16_task_01.crt
   ```
   ```text
   Country Name (2 letter code) [AU]:BY
   State or Province Name (full name) [Some-State]:SFO
   Locality Name (eg, city) []:Minsk
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:tech
   Organizational Unit Name (eg, section) []:devops
   Common Name (e.g. server FQDN or YOUR name) []:alexey.tech
   Email Address []:
   ```

3) Создал конфиг для Nginx в /etc/nginx/ssl/ssl.conf:

   ```text
   ssl_protocols TLSv1.2 TLSv1.3;
   ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
   ssl_prefer_server_ciphers on;
   ssl_session_cache shared:SSL:10m;
   ssl_session_timeout 10m;
   ```

4) Создал конфиг для Nginx в /etc/nginx/sites-available/lesson_16_task_01:

   ```text
   server {
     listen 443 ssl;
     server_name _;
     
     ssl_certificate /etc/nginx/ssl/lesson_16_task_01.crt;
     ssl_certificate_key /etc/nginx/ssl/lesson_16_task_01.key;
     
     include /etc/nginx/ssl/ssl.conf;
     
     add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
   
     root /var/www/lesson_16/task_01/;
     index index.html;
   
     location / {
       try_files $uri $uri/ =404;
     }
   }
   
   server {
     listen 80;
     server_name _;
     return 301 https://$host$request_uri;
   }
   ```

5) Создал символическую ссылку:

   ```bash
   sudo ln -s /etc/nginx/sites-available/lesson_16_task_01 /etc/nginx/sites-enabled/
   ```

6) Проверил конфиг Nginx на синтаксические ошибки и перезапустил Nginx:

   ```bash
   sudo nginx -t
   ```
   ```bash
   sudo nginx -s reload
   ```
   <img width="1141" height="142" alt="image" src="https://github.com/user-attachments/assets/f933d0e5-076a-46b4-85b1-f4e8ed117c3e" />

7) Открыл в браузере https://192.168.1.111:

   <img width="765" height="623" alt="image" src="https://github.com/user-attachments/assets/30c969dd-9c1c-43f5-85ef-43d416917177" />
