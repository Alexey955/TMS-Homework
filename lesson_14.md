## Задание 1
1) Сделал скрипт myscript.sh:

   ```bash
   #!/bin/bash
   exec 5> debug_output.txt
   BASH_XTRACEFD="5"
   PS4='$LINENO: '
   var="Привет мир!"
   # печать
   echo "$var"
   # альтернативный способ печати
   printf "%s\n" "$var"
   echo "Мой дом - это: $HOME"
   ```

2) Запустил скрипт в **режиме отладки**:

   <img width="574" height="197" alt="image" src="https://github.com/user-attachments/assets/484eb14f-2e85-40cc-8671-9fbab182099a" />


3) Изменил скрипт, чтобы **режим отладки** применялся к части скрипта:

   ```bash
   #!/bin/bash
   exec 5> debug_output.txt
   BASH_XTRACEFD="5"
   PS4='$LINENO: '

   set -x
   var="Привет мир!"
   # печать
   echo "$var"
   # альтернативный способ печати
   printf "%s\n" "$var"
   set +x

   echo "Мой дом - это: $HOME"
   ```

4) Запустил скрипт:

   <img width="566" height="154" alt="image" src="https://github.com/user-attachments/assets/af9d01f1-613e-4683-9c33-646f483481c5" />

5) Сделал скрипт myscript1.sh:

   ```bash
   #!/bin/bash
   var="Привет мир"
   echo "$var"
   echo "Мой домашний каталог is=$HOME
   echo "Мое имя is=$USER"
   ```

6) Запустил скрипт в **режиме проверки синтаксиса**:

   <img width="567" height="28" alt="image" src="https://github.com/user-attachments/assets/5b3668de-6565-433b-920b-569dd433bce2" />

7) Запустил скрипт в **подробном режиме**:

   <img width="572" height="114" alt="image" src="https://github.com/user-attachments/assets/cd586578-0cd4-4baa-a42f-94813eca1656" />

## Задание 2
1) Создал файл users:

   ```text
   user30 it
   user31 users
   user32 it
   user33 group33
   user34 it
   user35 users
   ```

2) Сделал скрипт myscript.sh:

   ```bash
   #!/usr/bin/env bash

   set -ue
   set -o pipefail

   # Checking root permissions
   if [ "$(id -u)" != 0 ]; then
     echo "root permissions required"
     exit 1
   fi

   # Variables
   file="users"
   shell="/sbin/nologin"
   oldIFS="$IFS"

   # Functions
   create_user() {

     # Restore IFS
     IFS="$oldIFS"

     # Create group if doesn't exist
     if ! grep -o "^${group}:x:" /etc/group &>/dev/null; then
       groupadd "$group"
     fi

     # Sudoers
     if [ "$group" = "it" ] || [ "$group" = "security" ]; then
       if ! grep "%$group" /etc/sudoers; then
         cp /etc/sudoers{,.bkp}
         echo '%'$group' ALL=(ALL) ALL' >> /etc/sudoers
       fi
     shell=/bin/bash
     elif [ "$user" = "admin" ]; then
       if ! grep "$user" /etc/sudoers; then
         cp /etc/sudoers{,.bkp}
         echo $user' ALL=(ALL) ALL' >> /etc/sudoers
       fi
       shell=/bin/bash
     fi

     # Create dir if doesn't exist
     if [ ! -d "/home/$group" ]; then
       mkdir /home/$group
     fi

     # Create user if doesn't exist
     if ! grep -o "^${user}:x:" /etc/passwd &>/dev/null; then
       useradd $user -g $group -b /home/$group -s $shell
     fi
   }

   # Check parameters
   if [ ${#} -eq 2 ]; then
     user=$1
     group=$2
     echo "Username: ${user}; Group: ${group}"
     create_user
   elif [ -f $file ]; then
     IFS=$'\n'
     for line in $(cat $file); do
       user=$(echo $line | cut -d' ' -f1)
       group=$(echo $line | cut -d' ' -f2)
       echo "Username: ${user}; Group: ${group}"
       create_user
     done
   else
     echo "Welcome!"
     select option in "Add user" "Show users" "Exit"; do
       case $option in
         "Add user")
           read -p "Print username: " user
           read -p "Print groupname: " group
           create_user ;;
         "Show users") tail -n 10 /etc/passwd ;;
         "Exit") break ;;
         *) echo "Wrong option" ;;
       esac
     done
   create_user
   fi
   ```

3) Запустил скрипт 1й раз:

   <img width="710" height="397" alt="image" src="https://github.com/user-attachments/assets/6db7dbf5-9741-41f3-966b-0c4934029d86" />

4) Удалил файл users и запустил скрипт 2й раз:

   <img width="698" height="346" alt="image" src="https://github.com/user-attachments/assets/e22592ab-7288-42b8-b5e7-26d398660b55" />
