## Задание 1
1) Получил список запланированных задач суперпользователя:
```bash
sudo crontab -l
```
<img width="537" height="46" alt="image" src="https://github.com/user-attachments/assets/1377d459-e573-44cc-ad40-21a819bc4175" />

2) Открыл файл crontab для редактирования:
```bash
sudo crontab -e
```
3) Добавил задание (строку в конец файла):
```cron
0 16 1 * * apt-get clean && apt-get autoclean
```
4) Проверил добавление задания:
```bash
sudo crontab -l
```
<img width="700" height="548" alt="image" src="https://github.com/user-attachments/assets/5e4f852d-76a3-440c-a6e9-7c5d1fbecd1e" />

## Задание 2
1) Установил nodejs:
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
```
```bash
sudo apt-get install -y nodejs
```
2) Проверил правильность установки:
```bash
nodejs -v
```
<img width="476" height="46" alt="image" src="https://github.com/user-attachments/assets/ad7a9fc3-fa34-49a3-bbce-bb0ba078c23a" />

3) Получил полный путь к nodejs:
```bash
which node
```
4) Добавил конфигурацию сервис-юнита (myapp.service) в директорию /etc/systemd/system:
```text
[Service]
Environment=MYAPP_PORT=3000
ExecStart=/usr/bin/node /usr/local/www/myapp/index.js
```
5) Создал директорию:
```bash
sudo mkdir -p /usr/local/www/myapp
```
6) Добавил скрипт с веб-сервером (index.js) в директорию из п.5:
```javascript
const http = require('http');
const MYAPP_PORT = process.env.MYAPP_PORT;

http.createServer((req, res) => {
	if(req.url === '/kill') {
		throw new ERROR('Someone kills me');
	}
	
	if(req.method === 'POST') {
		console.error(`Error: Request ${req.method} ${req.url}`);
		
		res.writeHead(405, {'Content-Type':'text/plain'});
		res.end('405 Method Not Allowed');
		
		return;
	}
	
	console.log(`Request ${req.method} ${req.url}`);
	
	res.writeHead(200, {'Content-Type':'text/plain'});
	res.end('200 OK');
}).listen(MYAPP_PORT);
```
7) Запустил сервис и проверил правильность запуска:
```bash
sudo systemctl start myapp.service
```
```bash
sudo systemctl status myapp.service
```
<img width="840" height="226" alt="image" src="https://github.com/user-attachments/assets/b6b63be1-e452-4763-a129-8067e311e4cb" />

8) Отправил GET-запрос для проверки работоспособности веб-сервера:
```bash
curl 'http://localhost:3000/'
```
<img width="768" height="46" alt="image" src="https://github.com/user-attachments/assets/d3ff454a-697d-437a-958d-e96e744bfe85" />

9) Добавил в конфигурацию сервис-юнита строку для автоматического перезапуска сервиса:
```text
Restart=always
```
10) Перезапустил юниты и сервис:
```bash
sudo systemctl daemon-reload
```bash
sudo systemctl restart myapp.service
```
11) Отправил GET-запрос, который приводит к остановке веб-сервера:
```bash
curl 'http://localhost:3000/kill'
```
12) Отправил GET-запрос для проверки работоспособности веб-сервера:
```bash
curl 'http://localhost:3000/'
```
<img width="765" height="44" alt="image" src="https://github.com/user-attachments/assets/52aa92db-3131-474d-b622-0c2e9c11ca9c" />

13) Получил пользователя и группу, от имени которых запущен процесс:
```bash
sudo systemctl show -p MainPID myapp.service | cut -d'=' -f2 | xargs -I{} ps -o user,group -p {}
```
<img width="1439" height="69" alt="image" src="https://github.com/user-attachments/assets/29f772d8-3e7e-4ef9-9b87-c3108d02ac73" />

14) Добавил в конфигурацию сервис-юнита строки для настройки пользователя и группы, от имени которых будет запущен процесс:
```bash
User=tms-user
Group=tms-user
```
15) Перезапустил юниты и сервис
16) Проверил изменение пользователя и группу, от имени которых запущен процесс:
```bash
sudo systemctl show -p MainPID myapp.service | cut -d'=' -f2 | xargs -I{} ps -o user,group -p {}
```
<img width="1444" height="67" alt="image" src="https://github.com/user-attachments/assets/966a3405-c6cf-47c9-b2c6-0462f181b337" />

17) Просмотрел логи в журнале systemd:
```bash
journalctl -u myapp.service
```
<img width="1142" height="333" alt="image" src="https://github.com/user-attachments/assets/78c15b5d-ce21-414e-954c-aeffb79f1aad" />

18) Добавил в конфигурацию сервис-юнита строки для настройки вывода процесса в syslog:
```text
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=myapp
```
19) Добавил конфигурацию syslog-демона (rsyslog) (100-myapp.conf) для настройки правил обработки логов (syslog) в директорию /etc/rsyslog.d/:
```text
$RepeatedMsgReduction off
if $programname=='myapp' then -/var/log/myapp/debug.log
```
20) Перезапустил юниты, rsyslog и сервис:
```bash
sudo systemctl daemon-reload
```
```bash
sudo systemctl restart rsyslog.service
```
```bash
sudo systemctl restart myapp.service
```
21) Просмотрел логи в syslog:
```bash
sudo cat /var/log/myapp/debug.log
```
<img width="868" height="89" alt="image" src="https://github.com/user-attachments/assets/83ac93ea-3944-4bdc-adfc-34cc00642943" />

22) Изменил конфигурацию сервис-юнита для вывода в разные файлы (в зависимости от уровня severity):
```text
[Service]
Environment=MYAPP_PORT=3000
ExecStart=/bin/sh -c '/usr/bin/node /usr/local/www/myapp/index.js | logger --tag myapp'
Restart=always
User=tms-user
Group=tms-user
StandardError=syslog
SyslogIdentifier=myapp
SyslogLevel=err
```
23) Изменил конфигурацию syslog-демона (100-myapp.conf) для вывода в разные файлы (в зависимости от уровня severity):
```text
$RepeatedMsgReduction off
if $programname=='myapp' and $syslogseverity < 5 then -/var/log/myapp/error.log
if $programname=='myapp' and $syslogseverity >= 5 then -/var/log/myapp/debug.log
```
24) Перезапустил юниты, rsyslog и сервис
25) Отправил GET-запрос и POST-запрос:
```bash
curl 'http://localhost:3000/'
```
```bash
curl -X POST http://localhost:3000/
```
26) Просмотрел логи в разных файлах:
```bash
sudo cat /var/log/myapp/debug.log
```
```bash
sudo cat /var/log/myapp/error.log
```
<img width="874" height="158" alt="image" src="https://github.com/user-attachments/assets/dfc6a6c9-b30c-4239-ba34-53cc227777b9" />
