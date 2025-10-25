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

2) Создал конфиг **static_config** для Nginx:

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

3) Создал **Dockerfile**:

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

## Задание 2
1) Создал файл **.env** с кредами БД:

   ```text
   DB_USER="myuser"
   DB_PASS="mysecretpassword"
   DB_NAME="mydb"
   ```

2) Создал файл **init.sql** для создания таблицы:

   ```sql
   create table mydb.public.my_counter (
     id serial primary key,
     add_datetime TIMESTAMP
   );
   ```

3) Создал файл **app.py** с кодом приложения:

   ```python
   from flask import Flask, jsonify
   import psycopg2
   import os
   
   app = Flask(__name__)
   
   DB_USER = os.getenv('DB_USER')
   DB_PASS = os.getenv('DB_PASS')
   DB_NAME = os.getenv('DB_NAME')
   
   try:
       # Установка соединения с БД
       conn = psycopg2.connect(
           host="db",
           database=DB_NAME,
           user=DB_USER,
           password=DB_PASS
       )
   
       # Создание курсора
       cur = conn.cursor()
   
   except psycopg2.Error as e:
       print(f"Ошибка базы данных: {e}")
   
   @app.route('/add')
   def add():
     cur.execute("insert into my_counter (add_datetime) values(now());")
     conn.commit()
     return "Success\n"
   
   @app.route('/get')
   def get():
     cur.execute("select max(id) from my_counter;")
     sql_result = cur.fetchall()
   
     return f"Counter value: {sql_result[0][0]}\n"
   
   @app.route('/health')
   def health_check():
     return jsonify({"status": "healthy"})
   
   if __name__ == '__main__':
     app.run(host='0.0.0.0', port=5000)
   ```

4) Создал файл **Dockerfile-app** для контейнеризации app.py:

   ```dockerfile
   FROM python:3.12-slim
   WORKDIR /app
   COPY ./app.py ./
   RUN apt update && apt install -y curl
   RUN python3 -m pip install Flask psycopg2-binary
   ENTRYPOINT ["python3", "app.py"]
   ```

5) Создал файл **default.conf** с конфигом nginx:

   ```text
   upstream backend {
     server app-py:5000;
   }
   
   server {
     listen 80;
     server_name _;
   
     location / {
             proxy_pass http://backend;
     }
   }
   ```

6) Создал файл **Dockerfile-nginx** для контейнеризации nginx:

   ```dockerfile
   FROM nginx:stable-alpine3.21
   RUN rm /etc/nginx/conf.d/default.conf
   COPY ./default.conf /etc/nginx/conf.d/default.conf
   ENTRYPOINT ["nginx", "-g", "daemon off;"]
   ```

7) Создал файл **docker-compose.yaml**:

   ```yaml
   services:
     db:
       image: postgres:17-alpine3.21
       container_name: db
       restart: always
       environment:
         POSTGRES_DB: ${DB_NAME}
         POSTGRES_USER: ${DB_USER}
         POSTGRES_PASSWORD: ${DB_PASS}
       healthcheck:
         test: ["CMD-SHELL", "sh -c 'pg_isready -U ${DB_USER} -d ${DB_NAME}'"]
         interval: 30s
         timeout: 10s
         retries: 5
       volumes:
         - ./init.sql:/docker-entrypoint-initdb.d/init.sql
         - pg_data:/var/lib/postgresql/data
   
     app-py:
       build:
         context: .
         dockerfile: Dockerfile-app
       container_name: app-py
       restart: always
       environment:
         DB_NAME: ${DB_NAME}
         DB_USER: ${DB_USER}
         DB_PASS: ${DB_PASS}
       depends_on:
         db:
           condition: service_healthy
       healthcheck:
         test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
         interval: 30s
         timeout: 10s
         retries: 5
   
     nginx:
       build:
         context: .
         dockerfile: Dockerfile-nginx
       container_name: nginx
       restart: always
       ports:
         - 80:80
       depends_on:
         app-py:
           condition: service_healthy
   
   volumes:
     pg_data:
   ```

8) Создал и запустил контейнеры:

   ```bash
   docker-compose up -d
   ```
   <img width="2158" height="445" alt="image" src="https://github.com/user-attachments/assets/fa90e625-52db-4ab8-925f-bf23a161c9b8" />

9) Отправил несколько запросов на добавление данных:

    ```text
    http://192.168.1.114/add
    ```
    <img width="409" height="114" alt="image" src="https://github.com/user-attachments/assets/cc3898e7-7efa-4c88-8389-c93193ee192a" />

10) Отправил запрос на получение данных:

    ```text
    http://192.168.1.114/get
    ```
    <img width="389" height="104" alt="image" src="https://github.com/user-attachments/assets/95c19b6b-e8df-4c79-b8d4-6e215d119b68" />

