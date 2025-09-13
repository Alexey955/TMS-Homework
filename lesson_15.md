## Задание 1
1) Создал файл для веб-страницы /var/www/lesson_15/task_01/index.html:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Студент: Алексей Васильев</p>
       <p>Тема занятия: WebServers</p>
   </body>
   </html>
   ```

3) Создал конфиг для Nginx в /etc/nginx/sites-available/lesson_15_task_01:

   ```text
   server {
     listen 80;
     server_name _;
   
     access_log /var/log/nginx/lesson_15_task_01_access.log;
     error_log /var/log/nginx/lesson_15_task_01_error.log;
   
     root /var/www/lesson_15/task_01/;
     index index.html;
   
     location / {
       try_files $uri $uri/ =404;
     }
   }
   ```

4) Создал символическую ссылку:

   ```bash
   sudo ln -s /etc/nginx/sites-available/lesson_15_task_01 /etc/nginx/sites-enabled/
   ```

5) Проверил конфиг Nginx на синтаксические ошибки и перезапустил Nginx:

   <img width="473" height="71" alt="image" src="https://github.com/user-attachments/assets/2139be94-8e99-4d34-9b32-bb10a68812e7" />

6) Добавил в конфиг /etc/hosts строку:

   ```text
   192.168.1.111	tms.by
   ```

7) Проверил, что домен резолвится:

   <img width="334" height="86" alt="image" src="https://github.com/user-attachments/assets/128487fe-3d95-45da-ac59-e983862aee6c" />


8) Открыл в браузере http://tms.by:

   <img width="534" height="247" alt="image_2025-09-12_18-03-53" src="https://github.com/user-attachments/assets/a5cf00ef-cf64-4470-81de-402d885c3b4b" />

## Задание 2
1) Установил Apache:

   ```bash
   sudo apt install -y apache2
   ```

2) Создал файл для веб-страницы /var/www/lesson_15/task_02/index.html:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
   </head>
   <body>
       <p>Тема занятия: WebServers</p>
       <p>Задание 2: настройка связки Nginx + Apache</p>
   </body>
   </html>
   ```

3) Создал конфиг для Apache в /etc/apache2/sites-available/lesson_15_task_02.conf:

   ```text
   <VirtualHost 127.0.0.1:10001>
     ServerName localhost
     DocumentRoot /var/www/lesson_15/task_02
     
     ErrorLog /var/log/apache2/lesson_15_task_02_error.log
     CustomLog /var/log/apache2/lesson_15_task_02_access.log combined
   </VirtualHost>
   ```

4) Изменил порт Apache в /etc/apache2/ports.conf:

   ```text
   listen 80 -> listen 10001
   ```

5) Создал символическую ссылку:

   ```bash
   sudo ln -s /etc/apache2/sites-available/lesson_15_task_02.conf /etc/apache2/sites-enabled/
   ```

6) Перезагрузил Apache и проверил, что указанный порт прослушивается:

   <img width="1019" height="51" alt="image" src="https://github.com/user-attachments/assets/9c7e53a4-fd5a-45a2-a658-c0e92f9a1dce" />

7) Создал конфиг для Nginx в /etc/nginx/sites-available/lesson_15_task_02:

   ```text
   server {
     listen 80;
     server_name _;
     
     access_log /var/log/nginx/lesson_15_task_02_access.log;
     error_log /var/log/nginx/lesson_15_task_02_error.log;
     
     location / {
       proxy_pass http://127.0.0.1:10001; 	  
     }
   }
   ```

8) Создал символическую ссылку:

   ```bash
   sudo ln -s /etc/nginx/sites-available/lesson_15_task_02 /etc/nginx/sites-enabled/
   ```

9) Проверил конфиг Nginx на синтаксические ошибки и перезапустил Nginx:

    <img width="472" height="70" alt="image" src="https://github.com/user-attachments/assets/554501c8-6a4c-41d2-a83b-be49ca0df76c" />

10) Открыл в браузере http://192.168.1.111:

    <img width="215" height="94" alt="image" src="https://github.com/user-attachments/assets/f0f3866f-6b07-480c-9654-0123b794c6ea" />
