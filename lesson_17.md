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
