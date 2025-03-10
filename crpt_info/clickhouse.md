# Ckickhouse. Replication

## требования для кластера ClickHouse

**Для данных:**  
click01.dkiselev.crpt.cloud

click02.dkiselev.crpt.cloud

**Для кипера:**  
keeper01.dkiselev.crpt.cloud

keeper02.dkiselev.crpt.cloud

keeper03.dkiselev.crpt.cloud

## На каждом узле выполнить команды  

```sh
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
curl -fsSL 'https://packages.clickhouse.com/rpm/lts/repodata/repomd.xml.key' | sudo gpg --dearmor -o /usr/share/keyrings/clickhouse-keyring.gpg

ARCH=$(dpkg --print-architecture)
echo "deb [signed-by=/usr/share/keyrings/clickhouse-keyring.gpg arch=${ARCH}] https://packages.clickhouse.com/deb stable main" | sudo tee /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update
```

## На каждом узле установить netcat (нужно для диагностики и выполнения команд кипера)

```sh
sudo apt-get install netcat
```

---

## На узлах click01.dkiselev.crpt.cloud и click02.dkiselev.crpt.cloud

```sh
sudo apt-get install -y clickhouse-server clickhouse-client
```

```sh
sudo systemctl start clickhouse-server.service
```

## На узлах keeper01.dkiselev.crpt.cloud, keeper02.dkiselev.crpt.cloud, keeper03.dkiselev.crpt.cloud устанавливаем clickhouse-keeper

```sh
sudo apt-get install -y clickhouse-keeper
```

## Файлы конфигурации

Выполняем все инструкции по заполнению файлов конфигурации из [официальной документации](https://clickhouse.com/docs/architecture/replication#editing-configuration-files)

## Запускаем на каждой ноде кипера  

```sh
sudo systemctl enable clickhouse-keeper
sudo systemctl start clickhouse-keeper
sudo systemctl status clickhouse-keeper
```

## На узлах click01.dkiselev.crpt.cloud или click02.dkiselev.crpt.cloud  

**Показать кластер:**

```sql
SHOW CLUSTERS
```

Результат

```sh
   ┌─cluster───────┐
1. │ cluster_1S_2R │
   └───────────────┘
```

**Получить информацию о кластере:**

```sql
SELECT *
FROM system.clusters
```

Результат

```sh
Query id: 9164c3ac-ffb9-4c1a-9296-0e613796dbea

Row 1:
──────
cluster:                 cluster_1S_2R
shard_num:               1
shard_name:              
shard_weight:            1
internal_replication:    1
replica_num:             1
host_name:               click01.dkiselev.crpt.cloud
host_address:            10.12.0.1
port:                    9000
is_local:                1
user:                    default
default_database:        
errors_count:            0
slowdowns_count:         0
estimated_recovery_time: 0
database_shard_name:     
database_replica_name:   
is_active:               ᴺᵁᴸᴸ
replication_lag:         ᴺᵁᴸᴸ
recovery_time:           ᴺᵁᴸᴸ

Row 2:
──────
cluster:                 cluster_1S_2R
shard_num:               1
shard_name:              
shard_weight:            1
internal_replication:    1
replica_num:             2
host_name:               click02.dkiselev.crpt.cloud
host_address:            10.12.0.3
port:                    9000
is_local:                0
user:                    default
default_database:        
errors_count:            0
slowdowns_count:         0
estimated_recovery_time: 0
database_shard_name:     
database_replica_name:   
is_active:               ᴺᵁᴸᴸ
replication_lag:         ᴺᵁᴸᴸ
recovery_time:           ᴺᵁᴸᴸ

2 rows in set. Elapsed: 0.007 sec. 
```

---

## На любой ноде кипера

**Помогает определить роль сервера в кластере:**

```sh
sudo echo mntr | nc localhost 9181 | awk '$1 == "zk_server_state" { print $2; }'
```

**Команда для получения состояния сервера ClickHouse Keeper:**

```sh
echo mntr | nc localhost 9181
```

Результат

```sh
zk_version v25.2.1.3085-stable-09989205d6fd661fb2683cbb1390fe4fcedaa548
zk_avg_latency 0
zk_max_latency 0
zk_min_latency 0
zk_packets_received 0
zk_packets_sent 0
zk_num_alive_connections 0
zk_outstanding_requests 0
zk_server_state leader
zk_znode_count 16
zk_watch_count 0
zk_ephemerals_count 2
zk_approximate_data_size 2850
zk_key_arena_size 0
zk_latest_snapshot_size 0
zk_open_file_descriptor_count 42
zk_max_file_descriptor_count 500000
zk_followers 2
zk_synced_followers 2
```

---

## список команд для мониторинга ClickHouse

### Базовые команды управления

**Перезагрузка демона systemd:**

```sh
sudo systemctl daemon-reload
```

**Старт, стоп, перезагрузка сервиса соответственно:**

```sh
sudo systemctl start clickhouse-server.service

sudo systemctl stop clickhouse-server.service

sudo systemctl restart clickhouse-server.service

sudo systemctl status clickhouse-server.service
```

**тоже самое для кипера:**

```sh
sudo systemctl start clickhouse-keeper.service

sudo systemctl stop clickhouse-keeper.service

sudo systemctl restart clickhouse-keeper.service

sudo systemctl status clickhouse-keeper.service
```

### Просмотр логов

```sh
sudo journalctl -eu clickhouse-server
sudo journalctl -u clickhouse-server -f --since "1 hour ago" --color=always | grep --color=always -E "ERROR|WARN|CRITICAL"
```

**для серверов с данными:**

```sh
sudo tail -f /var/log/clickhouse-server/clickhouse-server.log
sudo tail -f /var/log/clickhouse-server/clickhouse-server.err.log
```

**для серверов кипера:**

```sh
sudo tail -f /var/log/clickhouse-keeper/clickhouse-keeper.log
sudo tail -f /var/log/clickhouse-keeper/clickhouse-keeper.err.log
```

### Проверка конфигурации

**Для редактирования конфигурационных файлов (выбрать нужный):**

```sh
sudo nano /lib/systemd/system/clickhouse-server.service
sudo nano /etc/clickhouse-server/config.xml
sudo nano /etc/clickhouse-keeper/config.xml
sudo nano /etc/clickhouse-server/config.d/macros.xml
sudo nano /etc/clickhouse-server/config.d/network-and-logging.xml
sudo nano /etc/clickhouse-server/config.d/remote-servers.xml
sudo nano /etc/clickhouse-server/config.d/use-keeper.xml
sudo nano /etc/clickhouse-keeper/keeper_config.xml
```

**Проверка открытых портов:**

```sh
sudo ss -antpl | grep clickhouse
```

### Мониторинг ресурсов

**Просмотр использования CPU:**

```sh
top | grep clickhouse
```

**Мониторинг дискового пространства:**

```sh
df -h
```

**Скрипт для мониторинга процессов ClickHouse, показывает основные метрики производительности:**

```sh
{ echo "USER       PID     %CPU    %MEM    VSZ     RSS(GB)  TTY     STAT    START    TIME    COMMAND";  ps -aux | grep -i clickhouse | awk '{printf "%-10s %-10s %-10s %-10s %-10s %-10.2f %-10s %-10s %-10s %-10s %s\n", $1, $2, $3, $4, $5, $6/1024/1024, $7, $8, $9, $10, $11}'; } | cat | column -t
```

### SQL-запросы для мониторинга

**запрос для получения подробной информации о размере таблиц в ClickHouse:**

```sql
SELECT 
 database,
 table,
 formatReadableSize(sum(data_compressed_bytes)) AS compressed,
 formatReadableSize(sum(data_uncompressed_bytes)) AS uncompressed
FROM system.parts
WHERE active = 1
GROUP BY database, table
ORDER BY sum(data_uncompressed_bytes) DESC;
```

**Просмотр активных запросов:**

```sql
SELECT * FROM system.processes;
```
