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

## Задание 2
1) Получил список образов:

   ```bash
   docker images
   ```
   <img width="1048" height="285" alt="image" src="https://github.com/user-attachments/assets/3e724f51-65be-41f5-ae68-a4be62f4dd3c" />

2) Вывел информацию о слоях самого большого образа:

   ```bash
   docker inspect python-rest:0.2 | jq .[0].GraphDriver
   ```
   ```bash
   docker inspect python-rest:0.2 | jq .[0].RootFS
   ```
   <img width="2164" height="954" alt="image" src="https://github.com/user-attachments/assets/5b06576a-88bb-4e08-aa3e-bc3ba63854bd" />

3) Установил пакет dive для анализа слоев:

   ```bash
   DIVE_VERSION=$(curl -s "https://api.github.com/repos/wagoodman/dive/releases/latest" | grep -Po '"tag_name": "v\K[0-9.]+')
   ```
   ```bash
   curl -Lo dive.deb "https://github.com/wagoodman/dive/releases/latest/download/dive_${DIVE_VERSION}_linux_amd64.deb"
   ```
   <img width="1032" height="56" alt="image" src="https://github.com/user-attachments/assets/e7d9ea4f-7f7b-4c2d-a4ea-b02feb96cd41" />

4) Определил слой, который занимает больше всего места:

   ```bash
   dive python-rest:0.2
   ```
   <img width="1071" height="839" alt="image" src="https://github.com/user-attachments/assets/19175dd1-11c5-4a54-a9d1-ecf7e53441f9" />

5) Удалил ненужный образ:

   ```bash
   docker rmi python-rest:0.1
   ```
   <img width="1307" height="360" alt="image" src="https://github.com/user-attachments/assets/6f7d0abc-64a9-4a5c-9016-1065c0a9e8c5" />

6) Получить информацию по использованию файловой системы Docker’ом (до очистки):

   ```bash
   docker system df -v
   ```
   <img width="1795" height="791" alt="image" src="https://github.com/user-attachments/assets/90f338c2-5905-4d2b-88cc-1d199ba50f1e" />

7) Удалил ненужные образы, контейнеры, тома и сети:

   ```bash
   docker system prune -fa --volumes
   ```
   <img width="443" height="34" alt="image" src="https://github.com/user-attachments/assets/e9163faf-0488-4722-9afc-820d3ec30f64" />

8) Получить информацию по использованию файловой системы Docker’ом (после очистки):

   ```bash
   docker system df -v
   ```
   <img width="1744" height="645" alt="image" src="https://github.com/user-attachments/assets/7db7c53c-3196-498e-a1ac-ce3111c8d7cf" />

   
