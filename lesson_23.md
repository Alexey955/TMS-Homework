## Задание 1
1) Создал Docker volume:

   ```bash
   docker volume create nginx_volume
   ```
   <img width="657" height="81" alt="image" src="https://github.com/user-attachments/assets/51098960-8c6e-4a69-9c2a-b985f5c4e0c0" />


2) Запустил nginx в контейнере **nginx-01** с nginx_volume:

   ```bash
   docker run -d --name nginx-01 -v nginx_volume:/var/log/volume_log nginx:stable-alpine3.21
   ```

3) Создал Docker network:

   ```bash
   docker network create new_network
   ```
   <img width="665" height="170" alt="image" src="https://github.com/user-attachments/assets/7ccb9189-3ea7-4279-b3b7-86cfce74b3f2" />

## Задание 2
1) Запустил nginx в контейнере **nginx-02** с nginx_volume и new_network:

   ```bash
   docker run -d --name nginx-02 -v nginx_volume:/var/log/volume_log --network new_network nginx:stable-alpine3.21
   ```

2) Перешел в контейнер **nginx-01**:

   ```bash
   docker exec -it nginx-01 sh
   ```

3) Cоздал файл /var/log/volume_log/custom_log.txt:

   ```sh
   touch /var/log/volume_log/custom_log.txt
   ```

4) Cоздал канал FIFO /var/log/volume_log/syslog_fifo:

   ```sh
   mkfifo /var/log/volume_log/syslog_fifo
   ```

5) Настроил syslog для записи всех логов в syslog_fifo:

   ```sh
   echo '*.* /var/log/volume_log/syslog_fifo' > /etc/syslog.conf
   ```

6) Запустил syslog:

   ```sh
   syslogd
   ```

7) Запустил процесс, который читает из syslog_fifo и записывает в custom_log.txt и stdout процесса с id = 1

   ```sh
   tee /var/log/volume_log/custom_log.txt >> /proc/1/fd/1 < /var/log/volume_log/syslog_fifo &
   ```

8) Используя утилиту logger, записал несколько сообщений в custom_log.txt:

   ```sh
   logger "My custom message 1"
   logger "My custom message 2"
   logger "My custom message 3"
   ```

9) Вышел из контейнера **nginx-01** и убедился, что сообщения появились в лог-файле:

    ```bash
    exit

    docker logs -n 10 nginx-01
    ```
    <img width="1075" height="313" alt="image" src="https://github.com/user-attachments/assets/78c753fe-fe4f-47d3-8354-dff5f14b4353" />

10) Убедился, что сообщения появились в custom_log.txt и доступны в обоих контейнерах:

    ```bash
    docker exec nginx-01 cat /var/log/volume_log/custom_log.txt

    docker exec nginx-02 cat /var/log/volume_log/custom_log.txt
    ```
    <img width="1271" height="250" alt="image" src="https://github.com/user-attachments/assets/aaec2455-d2fa-4914-aae3-0340c04a2cc4" />

11) Остановил оба контейнера:

    ```bash
    docker stop nginx-01 nginx-02
    ```

12) Удалил оба контейнера:

    ```bash
    docker rm nginx-01 nginx-02
    ```

13) Удалил образ:

    ```bash
    docker rmi nginx:stable-alpine3.21
    ```

14) Удалил nginx_volume:

    ```bash
    docker volume rm nginx_volume
    ```

15) Удалил new_network:

    ```bash
    docker network rm new_network
    ```
