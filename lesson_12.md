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

## Задание 4
1) Создал каталоги и файлы:

   <img width="545" height="409" alt="image" src="https://github.com/user-attachments/assets/62eda97e-7165-4bb6-8dd6-642802ba8f10" />

2) Создал скрипт print-filenames-with-ext-to-file.sh:

   ```bash
   set -u

   # Check args length
   if [ ${#} -ne 3 ]; then
     echo "Usage: ${0} file folder extention"
     exit 1
   fi

   FILE_OUTPUT="$1"
   FOLDER_TO_SEARCH="$2"
   EXTENSION_TO_SEARCH="$3"

   # Check if FILE_OUTPUT exists
   if [ -f "$FILE_OUTPUT" ]; then
     rm "$FILE_OUTPUT"
   fi

   # Find files and put in array
   files_with_path_arr=($(find $FOLDER_TO_SEARCH -maxdepth 1 -name "*.${EXTENSION_TO_SEARCH}"))

   # Print filenames without paths to file
   counter=0
   for path in ${files_with_path_arr[@]}; do
     $(basename "$path" >> "$FILE_OUTPUT")
     ((counter++))
   done
   ```

3) Запустил скрипт 1й раз:

   <img width="1069" height="92" alt="image" src="https://github.com/user-attachments/assets/a26bf941-38cf-48bc-a6e8-f859f8c0b6d6" />

4) Запустил скрипт 2й раз:

   <img width="1116" height="136" alt="image" src="https://github.com/user-attachments/assets/ec35e3a2-712d-46ee-ab82-8a98f6182ceb" />

5) Запустил скрипт 3й раз:

   <img width="1099" height="136" alt="image" src="https://github.com/user-attachments/assets/9c9102a8-f0f3-44fd-b7e1-a87cd6ea8e2f" />

## Задание 5
1) Создал скрипт show-sizes-and-rules-for-files.sh:

   ```bash
   set -u

   # Check args length
   if [ ${#} -ne 1 ]; then
     echo "Usage: ${0} folder"
     exit 1
   fi

   # Check if folder
   if [ ! -d ${1} ]; then
     echo "Error: it isn't a folder"
     exit 1
   fi

   FOLDER_TO_SEARCH="$1"

   # Find files and put in array
   files_arr=($(find "$FOLDER_TO_SEARCH" -type f))

   # Print rules, sizes and filenames to cli
   for file in ${files_arr[@]}; do
     ls -lh ${file} | awk '{print $1,"\t"$5,"\t"$9}'
   done
   ```

2) Запустил скрипт:

   <img width="946" height="357" alt="image" src="https://github.com/user-attachments/assets/63ec3566-48fe-4024-9841-221ecfe3b3cb" />

