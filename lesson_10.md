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




```bash

```
