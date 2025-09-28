## Задание 1

```text
node-1: 192.168.1.111
node-2: 192.168.1.112
etcd:   192.168.1.113
```

1) Установил PostgreSQL на node-1 и node-2:

   ```bash
   sudo apt update
   
   sudo apt install curl ca-certificates
   
   sudo install -d /usr/share/postgresql-common/pgdg
   
   sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
   
   . /etc/os-release
   
   sudo sh -c "echo 'deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $VERSION_CODENAME-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
   
   sudo apt update
   
   sudo apt -y install postgresql
   ```
   

2) Настроил репликацию на node-1:

   psql:
   ```sql
   create role replicator with replication login password 'admin@123';
   ```
   ```sql
   select pg_create_physical_replication_slot('replica_slot');
   ```

   /etc/postgresql/18/main/pg_hba.conf:
   ```text
   host replication replicator 192.168.1.112/32 md5
   ```

   /etc/postgresql/18/main/postgresql.conf:
   ```text
   listen_addresses = 'localhost' -> listen_addresses = '0.0.0.0'
   wal_level = replica
   max_wal_senders = 10
   wal_keep_size = 1GB
   hot_standby = on
   ```

   ```bash
   sudo systemctl restart postgresql
   ```

3) Настроил репликацию на node-2:

   ```bash
   sudo systemctl stop postgresql
   ```

   /etc/postgresql/18/main/postgresql.conf:
   ```text
   listen_addresses = 'localhost' -> listen_addresses = '0.0.0.0'
   hot_standby = on
   ```

   ```bash
   sudo find /var/lib/postgresql/18/main -depth -mindepth 1 -delete
   ```
   ```bash
   sudo su - postgres
   
   pg_basebackup -h 192.168.1.111 -p 5432 -U replicator -D /var/lib/postgresql/18/main/ -Fp -Xs -P -R --slot=replica_slot
   ```
   ```bash
   sudo systemctl start postgresql
   ```

4) Убедился, что репликация работает, выполнив на node-1:

   ```sql
   select * from pg_stat_replication;
   ```
   <img width="2166" height="209" alt="image" src="https://github.com/user-attachments/assets/d905a190-9519-4a74-bc34-177c1f9087f1" />


5) Создал БД, таблицу и добавил данные на node-1:

   ```sql
   create databasqe orders;

   \c orders;

   create table servers_orders (id serial primary key, title varchar(255), cost int, status varchar(255), description text);

   insert into servers_orders (title, cost, status, description) values ('order-#1355', 2000, 'pending', 'Some order');
   ```

6) Убедился, что данные появились и на node-2:

   <img width="883" height="234" alt="image" src="https://github.com/user-attachments/assets/3752386e-d3f1-497a-b67c-c0567c3298b3" />

7) Установил ETCD на отдельном хосте:

    ```bash
   sudo apt update
   ```
   ```bash
   sudo apt install -y etcd-server
   ```

8) Настроил конфиг /etc/default/etcd:

   ```text
   ETCD_LISTEN_PEER_URLS="http://192.168.1.113:2380"
   ETCD_LISTEN_CLIENT_URLS="http://192.168.1.113:2379,http://127.0.0.1:2379"
   ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168.1.113:2380"
   ETCD_ADVERTISE_CLIENT_URLS="http://192.168.1.113:2379"
   ETCD_INITIAL_CLUSTER="default=http://192.168.1.113:2380"
   ETCD_INITIAL_CLUSTER_STATE="new"
   ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
   ETCD_NAME="default"
   ETCD_ENABLE_V2="true"
   ```

9) Перезапустил ETCD и проверил работоспособность:

   <img width="1777" height="117" alt="image" src="https://github.com/user-attachments/assets/bae6dadb-cc68-4652-9b6b-da99fe4d7f3f" />

10) Настроил Patroni:

    **На node-1 и node-2:**
    ```bash
    sudo ln -s /usr/lib/postgresql/18/bin/* /usr/sbin/
    ```
    ```bash
    sudo systemctl stop postgresql
    ```
    ```bash
    sudo apt install -y python3 python3-pip python3-testresources python3-psycopg2 patroni etcd-client
    ```
    ```bash
    sudo mkdir -p /data/patroni
    ```
    ```bash
    sudo chown -R postgres:postgres /data
    ```
    ```bash
    sudo chmod 700 /data/patroni
    ```
    **На node-1:**

    /etc/patroni/config.yml.in:
    ```yaml
    scope: postgres
    namespace: /db/
    name: node-1
    restapi:
      listen: 192.168.1.111:8008
      connect_address: 192.168.1.111:8008
    
    etcd:
      hosts: 192.168.1.113:2379
    
    bootstrap:
      dcs:
        ttl: 30
        loop_wait: 10
        maximum_lag_on_failover: 1048576
    
        postgresql:
          use_pg_rewind: true
          use_slots: true
          parameters:
    
      initdb:
      - encoding: UTF8
      - data-checksums
    
      pg_hba:
      - host replication replicator 127.0.0.1/32 md5
      - host replication replicator 192.168.1.111/0 md5
      - host replication replicator 192.168.1.112/0 md5
      - host all all 0.0.0.0/0 md5
    
      users:
        admin:
          password: admin
          options:
            - createrole
            - createdb
    
    postgresql:
      listen: 192.168.1.111:5432
      connect_address: 192.168.1.111:5432
      data_dir: /data/patroni
      pgpass: /tmp/pgpass
    
      authentication:
        replication:
          username: replicator
          password: admin@123
    
        superuser:
          username: postgres
          password: admin@123
    
      parameters:
          unix_socket_directories: '.'
    
    tags:
      nofailover: false
      noloadbalance: false
      clonefrom: false
      nosync: false
    ```
    **На node-2:**

    /etc/patroni/config.yml.in:
    ```yaml
    scope: postgres
    namespace: /db/
    name: node-2
    restapi:
      listen: 192.168.1.112:8008
      connect_address: 192.168.1.112:8008
    
    etcd:
      hosts: 192.168.1.113:2379
    
    bootstrap:
      dcs:
        ttl: 30
        loop_wait: 10
        maximum_lag_on_failover: 1048576
    
        postgresql:
          use_pg_rewind: true
          use_slots: true
          parameters:
    
      initdb:
      - encoding: UTF8
      - data-checksums
    
      pg_hba:
      - host replication replicator 127.0.0.1/32 md5
      - host replication replicator 192.168.1.111/0 md5
      - host replication replicator 192.168.1.112/0 md5
      - host all all 0.0.0.0/0 md5
    
      users:
        admin:
          password: admin
          options:
            - createrole
            - createdb
    
    postgresql:
      listen: 192.168.1.112:5432
      connect_address: 192.168.1.112:5432
      data_dir: /data/patroni
      pgpass: /tmp/pgpass
    
      authentication:
        replication:
          username: replicator
          password: admin@123
    
        superuser:
          username: postgres
          password: admin@123
    
      parameters:
          unix_socket_directories: '.'
    
    tags:
      nofailover: false
      noloadbalance: false
      clonefrom: false
      nosync: false
    ```
    **На node-1 и node-2:**

    /etc/systemd/system/patroni.service:
    ```text
    [Unit]
    Description=High availability PostgreSQL Cluster
    After=syslog.target network.target
    
    [Service]
    Type=simple
    User=postgres
    Group=postgres
    ExecStart=/usr/bin/patroni /etc/patroni/config.yml.in
    KillMode=process
    TimeoutSec=30
    Restart=no
    
    [Install]
    WantedBy=multi-user.targ
    ```
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start patroni
    ```

11) Убедился, что Patroni настроен:

    ```bash
    patronictl -c /etc/patroni/config.yml.in list
    ```
    <img width="1487" height="194" alt="image" src="https://github.com/user-attachments/assets/b6d37c8d-a5ab-4377-8c5b-477043e33b45" />

12) Отключил Patroni на node-1:

    <img width="1482" height="251" alt="image" src="https://github.com/user-attachments/assets/f46dc40d-4aa7-4cb3-ba26-ac7c9518554f" />

13) Включил Patroni на node-1:

    <img width="1480" height="247" alt="image" src="https://github.com/user-attachments/assets/94951fa2-27b8-41db-b5f2-f6e79101c2f2" />
