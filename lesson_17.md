## Задание 1
1) Установил MariaDB:

   ```bash
   sudo apt update && sudo apt install -y mariadb-server
   ```

2) Настроил пароль для MariaDB:

   ```bash
   sudo mysql_secure_installation
   ```
   ```text
   Enter current password for root (enter for none): Enter
   Switch to unix_socket authentication [Y/n]: n
   Change the root password? [Y/n] Y
   Остальное Enter
   ```

3) Залогинился и получил список БД:

   <img width="1125" height="571" alt="image" src="https://github.com/user-attachments/assets/bbba9829-8ca8-435d-a92e-9673ed7bc9ca" />

4) Добавил в /etc/mysql/debian.cnf пароль для пользователя root, залогинился без ввода пароля и получил список пользователей

   <img width="1125" height="511" alt="image" src="https://github.com/user-attachments/assets/a707e49c-b34f-48fb-b438-bd1fdce0637e" />

5) Сделал скрипт fill_db_med_center.sh для создания БД, таблиц и генерации данных:

   ```bash
   #!/usr/bin/env bash

   set -ueo pipefail
   
   # Проверка наличия sudo
   if [ $(id -u) -ne 0 ]; then
     echo "Error: need sudo"
     exit 1
   fi
   
   SQL_EXECUTE="mysql --defaults-file=/etc/mysql/debian.cnf"
   
   # Проверка Наличия БД medical_center
   if ${SQL_EXECUTE} -e "show databases;" | grep medical_center &>/dev/null; then
     read -p "DB medical_center exists, drop it? [y/N]: " answer
   
     case "$answer" in
       y)
         ${SQL_EXECUTE} -e "drop database medical_center;"
         ${SQL_EXECUTE} -e "create database medical_center;"
       ;;
       n) exit 0;;
       N) exit 0;;
       *) exit 0;;
     esac
   else
     ${SQL_EXECUTE} "create database medical_center;"
   fi
   
   # Создание таблиц
   ${SQL_EXECUTE} -D medical_center <<EOF
   create table analysis (
     an_id int auto_increment primary key,
     an_name varchar(100),
     an_cost int,
     an_price int,
     an_group int
   );
   
   create table groups (
     gr_id int auto_increment primary key,
     gr_name varchar(100),
     gr_temp int
   );
   
   create table orders (
     ord_id int auto_increment primary key,
     ord_datetime datetime,
     ord_an int
   );
   EOF
   
   # Добавление foreign keys
   ${SQL_EXECUTE} -D medical_center <<EOF
   alter table analysis add foreign key (an_group) references groups(gr_id);
   alter table orders add foreign key (ord_an) references analysis(an_id);
   EOF
   
   # Генерация случайного числа в диапазоне
   get_rand_num (){
     local MIN=$1
     local MAX=$2
     local RAND_VAL=$(od -An -N4 -tu4 < /dev/urandom)
   
     echo $(( RAND_VAL % ((MAX - MIN + 1)) + MIN ))
   }
   
   # Наполнение таблицы groups
   for i in {1..50}; do
     gr_temp=$(get_rand_num 0 15)
     ${SQL_EXECUTE} -D medical_center -e "insert into groups (gr_name, gr_temp) values (\"group_${i}\", ${gr_temp});"
   done
   
   # Наполнение таблицы analysis
   for i in {1..200}; do
     an_cost=$(get_rand_num 100 1000)
     an_price_rand=$(get_rand_num 10 20)
     an_price=$((${an_cost} + ${an_cost} / ${an_price_rand}))
     an_group=$(get_rand_num 1 50)
   
     ${SQL_EXECUTE} -D medical_center <<EOF
     insert into analysis (an_name, an_cost, an_price, an_group)
       values ("analysis_${i}", ${an_cost}, ${an_price}, ${an_group});
   EOF
   done
   
   # Наполнение таблицы Orders
   DATE_FROM_SEC=$(date -d "2018-01-01" +%s)
   DATE_TO_SEC=$(date -d "2022-01-01" +%s)
   for i in {1..5000}; do
     ord_datetime_sec=$(get_rand_num "$DATE_FROM_SEC" "$DATE_TO_SEC")
     ord_datetime=$(date -d @$ord_datetime_sec "+%Y-%m-%d %H:%M:%S")
     ord_an=$(get_rand_num 1 200)
   
     ${SQL_EXECUTE} -D medical_center <<EOF
     insert into orders (ord_datetime, ord_an)
       values ("${ord_datetime}", ${ord_an});
   EOF
   done
   ```

6) Запустил скрипт и проверил данные в БД:

   <img width="848" height="934" alt="image" src="https://github.com/user-attachments/assets/91e77f51-91d6-4735-a183-efff31d81a6c" />
   <img width="815" height="466" alt="image" src="https://github.com/user-attachments/assets/8c534a67-2b58-4516-9b80-3081ebdb6b7d" />

7) Выполнил запрос на получение названий и цен всех анализов, которые продавались 5 февраля 2020 и всю следующую неделю:

   <img width="1200" height="772" alt="image" src="https://github.com/user-attachments/assets/3e664ae1-aacb-498a-bb12-2e7c8be1144e" />

## Задание 3
2) Сделал скрипт db_bkp.sh:

   ```bash
   #!/usr/bin/env bash
   
   set -ueo pipefail
   
   # Проверка наличия sudo
   if [ $(id -u) -ne 0 ]; then
     echo "Error: need sudo."
     exit 1
   fi
   
   if [ $# -ne 3 ]; then
     echo "Usage 01: $0 --create database folder"
     echo "Usage 02: $0 --recover database file.sql"
     exit 1
   fi
   
   FLAG="$1"
   DATABASE="$2"
   SQL_DEFS="--defaults-file=/etc/mysql/debian.cnf"
   
   case $FLAG in
     "--create")
       # Проверка наличия каталога
       if [ ! -d "$3" ]; then
         echo "Error: folder $2 doesn't exist."
         exit 1
       fi
       # Проверка Наличия БД
       if ! mysql ${SQL_DEFS} -e "show databases;" | grep $2 &>/dev/null; then
         echo "Error: Database doesn't exist."
         exit 1
       fi
       TIMESTAMP=$( date +"%Y-%m-%d_%H-%M-%S")
       mysqldump "${SQL_DEFS}" "$DATABASE" > "${3}/${DATABASE}_${TIMESTAMP}.sql"
       echo "Backup ${DATABASE}_${TIMESTAMP}.sql for database $DATABASE created."
       sudo -u vboxuser scp "${3}/${DATABASE}_${TIMESTAMP}.sql" vboxuser@192.168.1.112:/home/vboxuser/tms-homework/lesson_17/task_03/
       echo "Backup is copied to 192.168.1.112:/home/vboxuser/tms-homework/lesson_17/task_03/${DATABASE}_${TIMESTAMP}.sql"
     ;;
     "--recover")
       # Проверка наличия файла
       if [ ! -f "$3" ]; then
         echo "Error: file.sql $2 doesn't exist."
         exit 1
       fi
       # Проверка Наличия БД
       if ! mysql ${SQL_DEFS} -e "show databases;" | grep $2 &>/dev/null; then
         mysql "${SQL_DEFS}" "$DATABASE" < "$3"
         echo "Database ${DATABASE} recovery completed."
         exit 0
       else
         mysql "${SQL_DEFS}" -e "drop database ${DATABASE};"
         echo "Database ${DATABASE} is dropped."
         mysql "${SQL_DEFS}" -e "create database ${DATABASE};"
         echo "Database ${DATABASE} is created."
         mysql "${SQL_DEFS}" ${DATABASE} < $3
         echo "Database ${DATABASE} recovery completed."
       fi
       ;;
     *)
       echo "Usage 01: $0 --create database folder"
       echo "Usage 02: $0 --recover database file.sql"
       exit 1
     ;;
   esac
   ```

2) Запустил скрипт для создания бэкапа:

   ```bash
   sudo bash db_bkp.sh --create medical_center /home/vboxuser/tms-homework/lesson_17/task_03
   ```
   На первой машине:
   <img width="2119" height="981" alt="image" src="https://github.com/user-attachments/assets/9633a548-6e02-47f0-b985-6571e9ace4d6" />

   На второй машине:
   <img width="1605" height="813" alt="image" src="https://github.com/user-attachments/assets/093f062c-df25-4c8d-b3a8-0358eb0bc0be" />

3) Удалил таблицу orders и создал таблицу table_new:

   <img width="1106" height="431" alt="image" src="https://github.com/user-attachments/assets/670b3c6b-9bb6-4539-b316-c435899c582c" />

4) Запустил скрипт для восстановления из бэкапа:

   ```bash
   sudo bash db_bkp.sh --recover medical_center /home/vboxuser/tms-homework/lesson_17/task_03/medical_center_2025-09-18_10-33-52.sql
   ```

   <img width="2163" height="140" alt="image" src="https://github.com/user-attachments/assets/96dda8cf-873d-4311-9c29-38d10f98da9d" />
   <img width="821" height="964" alt="image" src="https://github.com/user-attachments/assets/57f6e2bb-2e74-4160-8321-8e9cbb040c27" />

5) Добавил выполнение бэкапов в cron:

   ```text
   */5 * * * * /usr/bin/bash /home/vboxuser/tms-homework/lesson_17/task_03/db_bkp.sh --create medical_center /home/vboxuser/tms-homework/lesson_17/task_03
   ```
   На первой машине:
   <img width="1625" height="981" alt="image" src="https://github.com/user-attachments/assets/d8b2378e-5bce-43d4-b2e9-1d8657b592e3" />

   На второй машине:
   <img width="1621" height="924" alt="image" src="https://github.com/user-attachments/assets/ad2d8d23-f06a-47e9-9317-c26781fe9f90" />
