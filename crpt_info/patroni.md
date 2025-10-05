# Кластер Patroni

## Для установки необходимых компонентов выполните следующие действия

```sh
# Установите PostgreSQL 16:
sudo apt-get update
sudo apt-get install postgresql-16


# Установите Patroni:
sudo apt-get install patroni


# Установите etcd:
sudo apt-get install etcd

# Установите необходимые зависимости:
sudo apt-get install python3-pip python3-setuptools


# Установите дополнительные пакеты для Patroni
sudo apt-get install python3-psycopg2


# Создайте файл конфигурации для Patroni
sudo nano /etc/patroni.yml

# В этом файле укажите параметры кластера, такие как имя, адрес и параметры подключения к PostgreSQL.
```
