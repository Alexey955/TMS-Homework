## Задание 1
1) Обновил список пакетов:

   ```bash
   sudo apt update
   ```
2) Установил пакет для управления репозиториями:

   ```bash
   sudo apt install -y software-properties-common
   ```
3) Добавил репозиторий ppa:ansible/ansible в список источников apt:

   ```bash
   sudo apt-add-repository ppa:ansible/ansible
   ```
4) Обновил список пакетов:

   ```bash
   sudo apt update
   ```
5) Установил Ansible:

   ```bash
   sudo apt install -y ansible
   ```
## Задание 2
1) Сгенерировал пару ssh-ключей на Ansible-хосте:

   ```bash
   ssh-keygen -t rsa -b 4096
   ```
2) Добавил публичный ssh-ключ на хост-таргет:

   ```bash
   ssh-copy-id -i id_ed25519.pub vboxuser@192.168.1.112
   ```
## Задание 3
1) Создал файл hosts.txt со списком таргет-хостов:

   ```text
   [Ubuntu]
   target_01 ansible_host=192.168.1.112
   ```
2) Создал файл ansible.cfg с конфигурацией:

   ```text
   [defaults]
   host_key_checking = false
   inventory = ./hosts.txt
   ```

3) Убедился что Ansible установлен и конфигурация из п.2 применяется:

   ```bash
   ansible --version
   ```
   <img width="1622" height="281" alt="image" src="https://github.com/user-attachments/assets/13f5220f-d42a-4fd1-bcb5-397de56f09a1" />

4) Проверил доступность таргет-хостов:

   ```bash
   ansible all -m ping
   ```
   <img width="2150" height="306" alt="image" src="https://github.com/user-attachments/assets/dd44d18f-2f8a-4267-97f0-d8e99f1c3e0a" />

