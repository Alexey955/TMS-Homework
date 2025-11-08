## Задание 1
Чат используется для взаимодействия с проектом docker-compose. Сам проект описан в ДЗ 24 ([Задание 2](https://github.com/Alexey955/TMS-Homework/blob/main/lesson_24.md#%D0%B7%D0%B0%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-2))
1) Установил Java:
   ```bash
   sudo apt update
   sudo apt install -y fontconfig openjdk-21-jre
   java -version
   ```
   <img width="1202" height="112" alt="image" src="https://github.com/user-attachments/assets/120258b4-42ef-4a55-a783-4d9c139a3e0c" />

2) Установил Jenkins:
   ```bash
   sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   
   echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
      https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null

   sudo apt update

   sudo apt install jenkins
   ```

3) Получил пароль админа:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

4) Зашел в UI, создал админа, добавил credentials:
   ```text
   http://192.168.1.114:8080
   ```

   <img width="1694" height="219" alt="image" src="https://github.com/user-attachments/assets/7776eb4d-b10a-4623-873e-e2ab383322f7" />
5) Установил плагин "Telegram Bot".
6) Сгенерировал Токен API:
   ```text
   Jenkins -> Пользователь -> Security -> Токен API
   ```
   <img width="1078" height="284" alt="image" src="https://github.com/user-attachments/assets/bbee7a1e-72e5-4351-b7c5-4b6b7c3592d0" />

7) Настроил выполнение команд docker-compose для пользователя jenkins:

   Добьавил пользователя jenkins в группу docker
   ```bash
   sudo usermod -aG docker jenkins
   ```
   
   Создал домашнюю директорию:
   ```bash
   sudo mkdir /home/jenkins
   sudo chown jenkins:jenkins /home/jenkins
   ```

   Изменил домашнюю директорию в /etc/passwd:
   ```bash
   jenkins:x:111:111:Jenkins,,,:/var/lib/jenkins:/bin/bash -> jenkins:x:111:111:Jenkins,,,:/home/jenkins:/bin/bash
   ```

   Сделал содержимое директории /var/lib/jenkins доступным по пути /home/jenkins:
   ```bash
   sudo mount --bind /var/lib/jenkins /home/jenkins
   ```

   Указал что домашние директории пользователей могут находиться в каталоге /home/jenkins:
   ```bash
   sudo snap set system homedirs=/home/jenkins
   ```

   Перезапустил Jenkins:
   ```bash
   sudo systemctl restart jenkins
   ```

8) Создал pipeline в GitHub ([ссылка](https://github.com/Alexey955/TMS-Homework-29/blob/main/Jenkinsfile))
9) Создал pipeline **TMS-HW-29_Compose** в Jenkins:
   ```text
   Item: Pipeline
   
   Параметры:
     Имя: Action
       Тип: Choice Parameter
       Варианты: Compose ps, Compose start, Compose stop
   
   Definition: Pipeline script from SCM
     SCM: Git
       Repository URL: https://github.com/Alexey955/TMS-Homework-29.git
   
   Branch Specifier (blank for 'any'): */main
   ```
10) Создал файл **.env** с кредами:

    ```text
    TELEGRAM_BOT_TOKEN="xxx"
    JENKINS_URL="http://192.168.1.114:8080"
    JENKINS_USER="xxx"
    JENKINS_TOKEN="xxx"
    JOB_NAME="TMS-HW-29_Compose"

11) Создал файл **tm_bot.py** с кодом Telegram-бота:
    ```python
    import requests
    import telegram
    import os
    from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
    from telegram.ext import Application, CommandHandler, CallbackQueryHandler, ContextTypes
    
    TELEGRAM_BOT_TOKEN = os.getenv('TELEGRAM_BOT_TOKEN')
    JENKINS_URL = os.getenv('JENKINS_URL')
    JENKINS_USER = os.getenv('JENKINS_USER')
    JENKINS_TOKEN = os.getenv('JENKINS_TOKEN')
    JOB_NAME = os.getenv('JOB_NAME')
    
    async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
        keyboard = [
            [InlineKeyboardButton("❓ 01. Docker compose ps", callback_data='run_job_compose_ps')],
            [InlineKeyboardButton("🚀 02. Docker compose start", callback_data='run_job_compose_start')],
            [InlineKeyboardButton("🛑 03. Docker compose stop", callback_data='run_job_compose_stop')]
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
    
        await update.message.reply_text(
            "Выберите действие:",
            reply_markup=reply_markup
        )
    
    async def button_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
        query = update.callback_query
        await query.answer()
    
        if query.data in ('run_job_compose_ps', 'run_job_compose_start', 'run_job_compose_stop'):
            await run_jenkins_job(query, context)
    
    async def run_jenkins_job(query, context):
        try:
            await query.edit_message_text("🔄 Запускаю Jenkins job...")
    
            if query.data in ('run_job_compose_ps'):
                job_action = 'Compose ps'
            elif query.data == 'run_job_compose_start':
                job_action = 'Compose start'
            elif query.data == 'run_job_compose_stop':
                job_action = 'Compose stop'
    
            params = {
                'Action': job_action
            }
    
            job_url = f"{JENKINS_URL}/job/{JOB_NAME}/buildWithParameters"
    
            response = requests.post(
                job_url,
                auth=(JENKINS_USER, JENKINS_TOKEN),
                params=params,
            )
    
            if response.status_code in [200, 201]:
                await query.edit_message_text("✅ Job успешно запущен!")
            else:
                await query.edit_message_text(f"❌ Ошибка: {response.status_code}")
    
        except Exception as e:
            await query.edit_message_text(f"❌ Произошла ошибка: {str(e)}")
    
    def main():
        application = Application.builder().token(TELEGRAM_BOT_TOKEN).build()
    
        application.add_handler(CommandHandler("start", start))
        application.add_handler(CallbackQueryHandler(button_handler))
    
        print("Бот запущен...")
        application.run_polling()
    
    if __name__ == "__main__":
        main()
    ```

12) Создал файл **Dockerfile** для контейнеризации tm_bot.py:
    ```dockerfile
    FROM python:3.12-slim
    WORKDIR /app
    COPY ./tm_bot.py ./
    RUN python3 -m pip install requests python-telegram-bot
    ENTRYPOINT ["python3", "tm_bot.py"]
    ```

13) Создал файл **docker-compose.yaml** для tm_bot.py:
    ```yaml
    services:
      tm_bot_py:
        build:
          context: .
        container_name: tm_bot_py
        restart: always
        env_file:
          - .env
    ```

14) Создал и запустил контейнер с Telegram-bot:
    ```bash
    docker-compose -p tm_bot_py up -d
    ```
    <img width="1629" height="109" alt="image" src="https://github.com/user-attachments/assets/5986ac73-7082-4178-b2b2-612ccc04d7ef" />

15) Выбрал в чате "❓ 01. Docker compose ps":

    <img width="464" height="303" alt="image" src="https://github.com/user-attachments/assets/b2923ef4-4d5c-4a6b-9eff-7b2dd50ee90e" />
    <img width="535" height="256" alt="image" src="https://github.com/user-attachments/assets/0a56ecb0-de7c-4a53-a51c-1f3bc120fb8e" />

16) Выбрал в чате "🚀 02. Docker compose start":

    <img width="495" height="253" alt="image" src="https://github.com/user-attachments/assets/b29c0ddc-7652-4fe1-81e8-6d3bfd90e8d5" />
    <img width="2285" height="168" alt="image" src="https://github.com/user-attachments/assets/5810155d-2d1e-4376-95e7-2faf68831d8c" />
