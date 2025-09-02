## Задание 1
1) Создал text_file.txt:
   
   ```text
   Он --- серо-буро-малиновая редиска!!
   >>>:->
   А не кот.
   www.kot.ru
   ```

2) Сделал скрипт words-counter.sh:

   ```bash
   FILE_FOR_SEARCH="$1"

   # Check if file exists
   if [ ! -f "${FILE_FOR_SEARCH}" ]; then
     echo "Usage: $0 file"
     exit 1
   fi

   # Search mathces and put to array
   matches_arr=($(grep -o -P '(?:[а-яА-Яa-zA-Z]+-|(?:[а-яА-Яa-zA-Z]))+' ${FILE_FOR_SEARCH}))

   # Print number of matches to cli
   echo -e "Number of matches: ${#matches_arr[@]}\n"

   # Print each match to cli
   counter=1
   for match in ${matches_arr[@]}; do
     echo "Match ${counter}: $match"
     ((counter++))
   done
   ```

3) Запустил скрипт:

   <img width="890" height="269" alt="image" src="https://github.com/user-attachments/assets/5f310911-433b-4dfc-97b5-0e75ca3a056a" />

## Задание 2
1) Создал каталоги с файлами:

   <img width="770" height="427" alt="image" src="https://github.com/user-attachments/assets/62a95e07-1671-4a34-a655-2f8710a625f0" />

2) Создал локальный репозиторий:

   ```text
   git init
   ```

3) Добавил файлы в индекс и сделал коммит:

   ```text
   git add .
   ```
   ```text
   git commit -m "First commit"
   ```

4) Получил инфо по коммиту:

   <img width="692" height="134" alt="image" src="https://github.com/user-attachments/assets/69c0d445-d502-45b5-92c6-56dede06e2cf" />

5) Сделал скрипт change-filenames.sh:

   ```bash
   set -ue

   WORK_FOLDER="$1"

   # Check if folder exists
   if [ ! -d "$WORK_FOLDER" ]; then
     echo "Usage: $0 folder"
     exit 1
   fi

   find_files_with_ext() {
     local EXTENTION="$1"

     cd "$PWD"
     find "$WORK_FOLDER" -type f -name "*.${EXTENTION}";
   }

   rename_files() {
     local -n FILES_ARR="$1"
     local NEW_FILES_TEMPLATE="$2"

     cd "$PWD"
     for path in "${FILES_ARR[@]}"; do
       local filename_with_ext=$(basename $path)
       local filename_without_ext=$(echo "${filename_with_ext}" | sed -E 's/\.[a-z]+$//')
       local dirname=$(dirname $path)
       local new_file_template_result=${NEW_FILES_TEMPLATE/filename_without_ext/${filename_without_ext}}

       mv "$path" "${dirname}/${new_file_template_result}"
     done
   }

   # Get last commit timestamp
   PWD=$(pwd)
   cd "$WORK_FOLDER"
   # Check if commit exists
   if ! COMMIT_TIMESTAMP=$(git log -1 --format=%ct 2>/dev/null); then
     echo "Commit does not exist"
     exit 1
   fi

   # Find .log files and put to array
   log_files_arr=($(find_files_with_ext "log"))

   # Check if .log files exists
   if [ "${#log_files_arr[@]}" -ne 0 ]; then

     # Add commit timestamp to filenames .log files
     rename_files log_files_arr "filename_without_ext_${COMMIT_TIMESTAMP}.log"
   fi

   # Get last commit hash
   cd "$WORK_FOLDER"
   COMMIT_HASH=$(git rev-parse --short HEAD)

   # Find .py files and put to array
   py_files_arr=($(find_files_with_ext "py"))

   # Check if .py files exists
   if [ "${#py_files_arr[@]}" -ne 0 ]; then

     # Add commit hash to filenames .py files
     rename_files py_files_arr "filename_without_ext_${COMMIT_HASH}.py"
   fi
   ````

6) Выполнил скрипт:

   <img width="893" height="451" alt="image" src="https://github.com/user-attachments/assets/6c198fd1-3807-403f-b2b4-7a63c9e962f3" />
