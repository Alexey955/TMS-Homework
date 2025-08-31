## Задание 1
1) Создал каталог:

   <img width="474" height="69" alt="image" src="https://github.com/user-attachments/assets/cff040ae-5900-4f50-bf4d-70b9e3d185c4" />

## Задание 2
1) Установил инструменты для компиляции:

   ```bash
   sudo apt install -y build-essential
   ```

2) Создал файл 1.c:

   ```c
   #include <stdio.h>

   int main() {
     printf("HELLO Ubuntu\n");

     return 0;
   }
   ```

3) Скомпилировал:

   <img width="547" height="72" alt="image" src="https://github.com/user-attachments/assets/926fa5ef-9b1d-4083-92e8-4731450e1599" />

4) Запустил:

   <img width="461" height="47" alt="image" src="https://github.com/user-attachments/assets/aa3697d6-44b2-435c-b28e-1bac4a653c77" />

## Задание 3
1) Создал скрипт print-args-to-cli-and-file.sh:

   ```bash
   # Print args to cli
   echo "Arguments: $@"

   # Check if file exists
   FILE_WITH_ARGUMENTS="arguments.txt"
   if [ ! -f "$FILE_WITH_ARGUMENTS" ]; then
     touch "$FILE_WITH_ARGUMENTS"
   fi

   # Print args to file
   echo "Arguments: $@" > "$FILE_WITH_ARGUMENTS"
   ```

2) Запустил скрипт:

   <img width="860" height="91" alt="image" src="https://github.com/user-attachments/assets/ddc18e40-a32e-463a-a189-cee5ebe11b86" />

3) Проверил записи в файле arguments.txt:

   <img width="552" height="47" alt="image" src="https://github.com/user-attachments/assets/220e5bca-52a8-49e7-919c-aaf1883e439b" />

