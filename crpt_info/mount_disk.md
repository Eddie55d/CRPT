# Инструкция для доступа к диску c OC Ubuntu

Убедитесь, что у вас есть правильные учетные данные и доступ к сети.

## Необходимо скачать нужные пакеты

```sh
sudo apt update
sudo apt install samba -y
sudo apt-get install smbclient
sudo apt-get install cifs-utils
```

Проверьте, что Samba-сервер доступен и работает.

**Есть два варианта подключения:**

## 1. Подключиться через файловый менеджер Nautilus

для этого требуетя указать путь до диска и на странице подключения ввести логин и пароль (как в AD), домен (CRPT)

## 2. Смонтировать в командной строке

Сoздать папку для монтирования, например:

```sh
sudo mkdir /mnt/disk_o
sudo chmod 755 /mnt/disk_o
```

смонтировать диск:

```sh
export USER='your_username'
export PASSWORD='your_password'
sudo mount -t cifs //fileshare/DEP/OP/ГЭБД /mnt/disk_o -o username=$USER,password=$PASSWORD,workgroup=CRPT,sec=ntlmv2
```

После выполнения команды проверьте, что диск смонтирован успешно:

```sh
df -h | grep /mnt/disk_o
```

## Автоматическое монтирование при загрузке

Если вы хотите, чтобы диск монтировался автоматически при загрузке системы, добавьте запись в файл /etc/fstab. Для повышения безопасности рекомендуется использовать файл с учетными данными.

### Создайте файл с учетными данными

```sh
sudo nano /etc/smbcredentials
```

Добавьте в него следующие строки:

```ini
username=your_username
password=your_password
```

Установите права доступа к файлу:

```sh
sudo chmod 600 /etc/smbcredentials
```

Измените запись в fstab:

```sh
//fileshare/DEP/OP/ГЭБД /mnt/disk_o cifs credentials=/etc/smbcredentials,workgroup=CRPT,sec=ntlmv2 0 0
```

## Удаление монтирования

Если вам нужно размонтировать диск, выполните следующую команду:

```sh
sudo umount /mnt/disk_o
```
