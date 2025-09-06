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
