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

   
