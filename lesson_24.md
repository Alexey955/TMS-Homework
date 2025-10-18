## Задание 1
1) Создал файлы с веб-страницами:

   **./static/index.html:**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Main page</p>
   </body>
   </html>
   ```

   **./static/page_1.html:**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Page 1</p>
   </body>
   </html>
   ```

   **./static/page_2.html:**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Page 1</p>
   </body>
   </html>
   ```

2) Создал конфиг static_config для Nginx:

   ```text
   server {
     listen 80;
     server_name _;
   
     access_log /var/log/nginx/lesson_24_task_01_access.log;
     error_log /var/log/nginx/lesson_24_task_01_error.log;
   
     root /app/static/;
     index index.html;
   
     location / {
       try_files $uri $uri/ =404;
     }
   }
   ```

3) Создал Dockerfile:

   ```dockerfile
   FROM ubuntu:22.04
   RUN apt-get update && apt-get install -y nginx curl vim
   RUN mkdir -p /app/static
   COPY ./static ./app/static
   RUN rm /etc/nginx/sites-enabled/default
   COPY ./static_config ./etc/nginx/sites-enabled/
   EXPOSE 80
   ENTRYPOINT ["nginx", "-g", "daemon off;"]
   ```

4) Создал образ:

   ```bash
   docker build -t nginx-custom:0.1 .
   ```
   <img width="2155" height="530" alt="image" src="https://github.com/user-attachments/assets/fa5b3208-aa6f-431a-b474-1208b73c05de" />

5) Запустил контейнер:

   ```bash
   docker run -d -it -p 8080:80 --name nginx-01 nginx-custom:0.1
   ```
   <img width="2010" height="170" alt="image" src="https://github.com/user-attachments/assets/b5f28cca-b9db-4d91-874a-1316cab6d7ea" />

6) Проверил что страницы доступны:

   **1:**
   
   <img width="405" height="106" alt="image" src="https://github.com/user-attachments/assets/6d310302-de51-40c5-b202-f4253ab65019" />


   **2:**
   
   <img width="465" height="105" alt="image" src="https://github.com/user-attachments/assets/1afb029a-01b4-4cc6-ba2e-2afe4f629a17" />


   **3:**
   
   <img width="459" height="106" alt="image" src="https://github.com/user-attachments/assets/336db41e-7232-4733-8374-7cf1a7604c18" />



