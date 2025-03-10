# LINUX COMMANDS  

## инфо OS

```sh
uname -a
```

```sh
cat /etc/os-release
```

## info CPU

```sh
cat /proc/cpuinfo
```

```sh
lscpu
```

---

## Отключить графический интерфейс  

Для того чтобы увидеть цель, к которой система загружается по умолчанию, запустите подкоманду:  

```sh
systemctl get-default
```

Большинство дистрибутивов Linux загружаются по умолчанию в модуль **graphical.target**,  
что не подходит для серверов, которым не нужен графический интерфейс.

**Для изменения:**

```sh
sudo systemctl set-default multi-user.target
```

**Чтобы увидеть все доступные цели системы, запустите системные списки:**

```sh
systemctl list-units --type = target
```

**Добавление пользователя в группу sudo:**

```sh
sudo visudo
```  

Перейдите в раздел, где определяются права отдельных пользователей.

**Чтобы предоставить полный доступ:**

```sh
username ALL=(ALL:ALL) ALL
```

**Полный доступ без ввода пароля:**

```sh
username ALL=NOPASSWD: ALL
```  

For nano (default for some Debian installations): CTRL + O to write changes, then Enter, and CTRL + X to exit.

## Если не устанавливаются пакеты в Debian 12

```sh
sudo vi /etc/apt/sources.list
```

вверху закомментируйте строку, пример:
**#cdrom://[Debian GNU/Linux 12.1.0 _Bookworm_ - Official amd64 DVD Binary-1 with firmware 20230722-10:49]**

```sh
sudo apt-get update
```

## Поменять имя хоста

```sh
hostnamectl set-hostname hostname
```

```sh
hostnamectl set-hostname "hostname" --pretty
```

## Поменять таймзону

```sh
sudo timedatectl set-timezone Europe/Moscow  
```

## Отобразить сетевые подключения

```sh
sudo nmcli connection show
```

## Просмотр текущего состояния сети

```sh
nmcli device status
```

## Проверка открытых портов

```sh
sudo ss -tunlp
```

- `-t` — отображение TCP соединений,
- `-u` — отображение UDP соединений,
- `-n` — отображение числовых адресов,
- `-l` — отображение прослушивающих сокетов,
- `-p` — отображение идентификаторов процессов и имен программ.

## Проверка открытых портов с netstat

```sh
sudo netstat -tunpl
```

## Сканирование портов

```sh
nc -zv localhost 1-65535 2>&1 | grep succeeded
```

- `-z` — режим сканирования без ввода-вывода,
- `-v` — подробный вывод,
- `2>&1` — перенаправление вывода,
- `grep succeeded` — фильтрация успешных подключений.

## Проверка доступности хоста

```sh
ping [hostname или IP-адрес]
```

## Проверка открытых портов c lsof

**lsof** - команда для отображения списка открытых файлов, которая также может использоваться для проверки открытых портов.

```sh
sudo lsof -nP -iTCP -sTCP:LISTEN
```

**Для проверки конкретного порта:**

```sh
sudo lsof -nP -i:5054
```

## Сканирование всех открытых портов

**nmap** - это утилита для сканирования сети, которая позволяет проверять открытые порты на локальных и удаленных системах.

```sh
sudo nmap -n -PN -sT -sU -p- localhost
```

- `-n` — пропуск DNS-разрешения,
- `-PN` — пропуск фазы обнаружения,
- `-sT` и `-sU` — сканирование TCP и UDP портов соответственно,
- `-p-` — сканирование всех портов.

---

## основные команды для работы с логами в Linux

### Kоманды просмотра логов

**Просмотр всего содержимого лог-файла:**

```sh
sudo cat /var/log/syslog
```

**Постраничный просмотр с возможностью поиска:**

```sh
less /var/log/syslog
```

**Просмотр последних строк лога:**

```sh
tail /var/log/syslog
```

**Просмотр последних 50 строк:**

```sh
tail -n 50 /var/log/syslog
```

**Мониторинг лога в реальном времени:**

```sh
tail -f /var/log/syslog
```

### Поиск по логам с grep

**Простой поиск по слову:**

```sh
grep "error" /var/log/syslog
```

**Поиск с учетом регистра:**

```sh
grep -i "warning" /var/log/syslog
```

**Инвертированный поиск (все строки, НЕ содержащие слово):**

```sh
grep -v "info" /var/log/syslog
```

**Комбинированный поиск:**

```sh
grep -iE "error|warning" /var/log/syslog
```

**Фильтрация и сортировка:**

```sh
grep "error" /var/log/syslog | sort | uniq -c
```

### Работа с системными журналами через journalctl

```bash
# Просмотр системного журнала
journalctl

# Фильтрация по уровню важности
journalctl -p err

# Просмотр логов конкретного сервиса
journalctl -u ssh

# Просмотр логов за определенный период
journalctl --since "today"
journalctl --since "1 hour ago"
journalctl --since "2025-03-06"

### Поиск критических ошибок
sudo journalctl -p err -b

# Просмотр системных сообщений
```sh
sudo journalctl -xb --since "1 hour ago"

```

### Просмотр сообщений ядра

**Вывод всех сообщений ядра:**

```sh
dmesg
```

**Вывод с полной временной меткой и заданным уровнем сообщения:**

```sh
sudo dmesg -T -l crit
```

**Просмотр последних сообщений ядра:**

```sh
dmesg | tail
```

**Вывод сообщений в реальном времени:**

```sh
dmesg -w
```

### Мониторинг безопасности

**Просмотр попыток авторизации:**

```sh
tail -f /var/log/auth.log
```

**Поиск неудачных попыток входа:**

```sh
sudo grep "Failed password" /var/log/auth.log
```
