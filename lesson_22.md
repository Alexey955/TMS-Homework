## Задание 1
1) Установил Docker:

   ```bash
   curl -fsSl https://get.docker.com -o get-docker.sh
   ```
   ```bash
   sudo sh get-docker.sh
   ```
   <img width="645" height="57" alt="image" src="https://github.com/user-attachments/assets/a117860a-9096-4aa7-91bc-4b6a61029d39" />

2) Настроил вызов команд Docker без sudo:

   ```bash
   sudo usermod -aG docker vboxuser
   ```

3) Запустил mysql в контейнере:

   ```bash
   docker run --name=mysql -v /var/lib/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=kfl2b7i0kf -p 3306:3306 -d mysql:oraclelinux9
   ```
   <img width="2205" height="85" alt="image" src="https://github.com/user-attachments/assets/d972d591-fb82-4510-8de0-1c32ac128d6a" />

4) Перешел в интерактивный режим контейнера и подключился к БД:

   ```bash
   docker exec -it mysql bash
   ```
   ```bash
   mysql -u root -pkfl2b7i0kf
   ```
   <img width="1181" height="731" alt="image" src="https://github.com/user-attachments/assets/fc1cfdd4-2aef-45f8-9b0f-b857cc68659a" />

5) Остановил контейнер:

   ```bash
   docker stop mysql
   ```
   <img width="1706" height="254" alt="image" src="https://github.com/user-attachments/assets/36d90423-195c-4d81-85d8-8f54340851c0" />

6) Удалил контейнер:

   ```bash
   docker rm mysql
   ```
   <img width="999" height="168" alt="image" src="https://github.com/user-attachments/assets/54ded97f-9d6c-490c-990c-293b75cbc71c" />
