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
