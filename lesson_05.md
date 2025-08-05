## Задание 1
1) Импортировал публичный ключ:
```bash
sudo apt-get install gnupg curl

curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
```
2) Создал файл списка:
```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```
3) Перезагрузил локальную БД пакетов:
```bash
sudo apt-get update
```
4) Установил MongoDB:
```bash
sudo apt-get install -y mongodb-org
```
5) Запустил MongoDB:
```bash
sudo systemctl start mongod
```
6) Проверил правильность установки:
```bash
sudo systemctl status mongod
```
<img width="881" height="226" alt="image" src="https://github.com/user-attachments/assets/94fa083d-1d44-4ca9-b64a-52062bc66089" />

7) Подключился к MongoDB (как admin):
```bash
mongosh
```
8) Создал БД mydatabase:
```bash
use mydatabase
```
9) Создал таблицу data:
```javascript
db.createCollection("data")
```
10) Добавил данные в таблицу data:
```javascript
db.data.insertOne({test: "value"})
```
11) Создал пользователя manager с правами только на чтение:
```javascript
db.createUser({
  user: "manager",
  pwd: "{Password}",
  roles: [
    {
      role: "read",
      db: "mydatabase",
      collection: "data"
    }
  ]
})
```
12) Вышел из MongoDB:
```bash
exit
```
13) Настроил обязательную авторизацию для MongoDB:
    1) Добавил в файл /etc/mongod.conf:
    ```bash
    security:
      authorization: enabled
    ```
    2) Перезагрузил MongoDB:
    ```bash
    sudo systemctl restart mongod
    ```
14) Подключился к MongoDB под пользователем manager
```bash
mongosh -u manager -p {Password} --authenticationDatabase mydatabase
```
15) Переключился на БД mydatabase
```bash
use mydatabase
```
16) Получил данные из таблицы data:
```javascript
db.data.find()
```
<img width="653" height="157" alt="image" src="https://github.com/user-attachments/assets/fba34e81-8da6-4b51-b5c2-9d73fc959a52" />

17) Попробовал добавить данные в таблицу data:
```javascript
db.data.insertOne({test: "value"})
```
<img width="1214" height="90" alt="image" src="https://github.com/user-attachments/assets/004df5a2-5b47-409e-b98a-1fc7cad2e792" />
