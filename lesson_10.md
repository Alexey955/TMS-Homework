## Задание 1
1) Установил vim:
   ```bash
   sudo apt install vim -y
   ```
2) Создал директорию practice и перешел в нее:
   ```bash
   mkdir practice
   ```
   ```bash
   cd practice
   ```
3) Создал файл memo:
   ```bash
   vim memo
   ```
4) Перешел в режим "Ввода":
   ```text
   i
   ```
5) Ввел текст:
   ```text
   @REM AUTOEXEC.BAT DTK 386/40
   ECHO OFF
   Path c:\dos;c:\stacker;c:\Util;c:\NC;C:\MOUSE
   SET PROMPT=$P$G
   SET TMP=C:\TEMP
   LH C:\UTIL\RKEGA
   goto %config%
   :student1
   C:\DOS\SMARTDRV.EXE C+ 2048 1024
   goto nc
   :student2
   APPEND E:\tc\bgi
   :teacher
   PATH %path%E:\windows;e:\tc;e:\tc\bin;e:\foxpro;
   goto win
   :ONC
   PATH %path%G:\pctcp;
   SET TZ=GMT
   goto nc
   :nc
   nc.exe
   goto end
   win.com
   :end
   ```
6) Перешел в "Командный режим":
   ```text
   Esc
   ```
7) Сохранил файл и вышел из него:
   ```text
   :wq
   ```
8) Проверил что в файле появились записи:

   <img width="509" height="552" alt="image" src="https://github.com/user-attachments/assets/32f93e9f-a7ed-4fb6-b11f-99fb8eb04f50" />

## Задание 2
1) Открыл файл memo:
   ```bash
   vim memo
   ```
2) Отредактировал файл.
3) Сохранил файл и вышел из него:
   ```text
   :wq
   ```
4) Проверил что файл изменился:

   <img width="502" height="618" alt="image" src="https://github.com/user-attachments/assets/cc36695d-7378-45bd-86c7-abcbe767e90e" />

## Задание 3
1) Вернулся в домашний каталог:
   ```bash
   cd ..
   ```
2) Создал файл testcase.c в домашнем каталоге и скопировал в него [текст](https://gist.github.com/AnastasiyaGapochkina01/9544aee4c5d745a6002ba39c6c71b38d).
3) Открыл файл testcase.c:
   ```bash
   vim testcase.c
   ```
4) Включил отображение номеров строк:
   ```bash
   :set number
   ```
   <img width="551" height="226" alt="image" src="https://github.com/user-attachments/assets/e9d50c12-ae5b-405e-9659-6624d37340ad" />
5) Заменил слово WORD на IGNORE:
   ```bash
   :%s/WORD/IGNORE/gc
   ```
6) Заменил слово Reset на set:
   ```bash
   :%s/Reset/set/gc
   ```
7) Заменил слово input на output:
   ```bash
   :%s/input/output/gc
   ```
8) Скопировал строки с 16 по 29 в файл printwords.c:
   ```bash
   :16,29y
   ```
   ```bash
   :sp printwords.c
   ```
   ```bash
   p
   ```
   ```bash
   :wq
   ```
9) Удалил 2 последние строки:
   ```bash
   2dd
   ```
10) Перенес фрагмент текста:
    ```bash
    9dd
    ```
    ```bash
    p
    ```
11) Запишисал изменения в файл testvim.c:
    ```bash
    :sav testvim.c
    ```
12) Вышел из редактора:
    ```text
    :q!
    ```
13) Проверил что файл testcase.c не изменился:

    <img width="598" height="577" alt="image" src="https://github.com/user-attachments/assets/c04e27cb-a875-4857-915f-0167ab966b63" />
    <img width="659" height="781" alt="image" src="https://github.com/user-attachments/assets/5a6f2dc4-5401-4e05-bd59-78beb3d4ec49" />
    <img width="588" height="690" alt="image" src="https://github.com/user-attachments/assets/3a40c4de-c358-4438-a9f2-f6d59c2b0d02" />

14) Проверил что файл printwords.c содержит скопированные строки:

    <img width="616" height="334" alt="image" src="https://github.com/user-attachments/assets/325eabb9-eca6-4ab4-b2e8-1b8dc608fde3" />

16) Проверил что файл testvim.c содержит изменения:

    <img width="626" height="666" alt="image" src="https://github.com/user-attachments/assets/6d12195c-63a1-4822-add3-27f0f15efaf1" />
    <img width="661" height="693" alt="image" src="https://github.com/user-attachments/assets/2edc0c0d-58cc-4799-8bc1-1977bb243674" />
    <img width="603" height="642" alt="image" src="https://github.com/user-attachments/assets/bf45eb70-0331-426c-b349-6edb310c0793" />



```bash

```
