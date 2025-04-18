
# MongoDB

## Что надо понимать и уметь:

## 1. Что такое реплика сет?

**Реплика-сет (Replica Set) в MongoDB** - это группа экземпляров MongoDB, которые поддерживают один и тот же набор данных. Реплика-сет обеспечивает высокую доступность и отказоустойчивость, а также позволяет выполнять резервное копирование и восстановление данных. 

**Основные компоненты Replica Set:**  
- **Primary** (Основной) - это основной экземпляр, который принимает все записи данных. Все операции записи происходят на этом экземпляре. Он отвечает за обработку запросов на запись и обновление данных.

- **Secondary** (Вторичный) - это один или несколько экземпляров, которые получают данные от основного экземпляра. Они могут использоваться для чтения данных (если это разрешено) и служат резервными копиями основного экземпляра. Вторичные экземпляры синхронизируются с основным экземпляром, получая от него изменения.

- **Arbiter** (Арбитр) - это специальный экземпляр, который не хранит данные, но участвует в процессе выборов. Арбитры используются для обеспечения четного числа голосов в процессе выборов, что помогает избежать ситуации, когда два экземпляра могут стать основными одновременно.

**Основные функции реплика-сета**  
- **Отказоустойчивость**  
Если основной экземпляр выходит из строя, один из вторичных экземпляров может быть автоматически повышен до основного, что обеспечивает непрерывность работы.

- **Синхронизация данных**  
Вторичные экземпляры постоянно синхронизируются с основным экземпляром, получая изменения в реальном времени. Это позволяет поддерживать актуальность данных на всех экземплярах.

- **Чтение из вторичных экземпляров**  
В зависимости от конфигурации, можно настроить чтение данных из вторичных экземпляров, что может помочь распределить нагрузку и улучшить производительность.

- **Автоматическое восстановление**  
MongoDB автоматически восстанавливает реплика-сет после сбоя, повышая один из вторичных экземпляров до основного, если это необходимо.

- **Геораспределенные реплика-сеты**  
Реплика-сеты могут быть развернуты в разных географических регионах, что позволяет обеспечить доступность данных даже в случае сбоя в одном из регионов.



## 2. Что такое шардирование?

**Шардирование** в MongoDB — это метод распределения данных по нескольким серверам (шардам) для обеспечения горизонтальной масштабируемости и повышения производительности. Шардирование позволяет обрабатывать большие объемы данных и высокие нагрузки, распределяя их по различным узлам кластера. 

### Основные компоненты шардирования:

- **Шарды** (Shards): Это отдельные экземпляры MongoDB, которые хранят часть данных. Каждый шард может быть реплика-сетом, что обеспечивает отказоустойчивость и высокую доступность.

- **Маршрутизатор** (mongos): Это компонент, который управляет запросами к шардированному кластеру. Он принимает запросы от клиентов и направляет их к соответствующим шартам. Клиенты взаимодействуют с маршрутизатором, а не напрямую с шардом.

- **Конфигурационный сервер** (Config Server): Это серверы, которые хранят метаданные о шардированном кластере, такие как информация о шардировании, маршрутизации и состоянии шардов. Конфигурационные серверы также могут быть реплика-сетом для повышения доступности.

### Принципы работы шардирования

**Шардирование по ключу:**   
Данные распределяются по шартам на основе определенного ключа шардирования. Этот ключ выбирается при создании шардированного коллекции и определяет, как данные будут распределены. Например, если ключом шардирования является поле user_id, то все документы с одинаковым user_id будут храниться на одном и том же шарде.

**Балансировка нагрузки:**  
MongoDB автоматически распределяет данные между шардированными экземплярами, чтобы избежать перегрузки одного из них. Если один шард становится слишком загруженным, MongoDB может переместить часть данных на другой шард.

**Запросы:**  
Когда клиент отправляет запрос, маршрутизатор (mongos) определяет, к какому шардированному экземпляру направить запрос, основываясь на ключе шардирования. Если запрос не содержит ключа шардирования, маршрутизатор может направить его ко всем шартам, что может привести к увеличению времени выполнения.

### Преимущества шардирования  
- Горизонтальная масштабируемость: Шардирование позволяет добавлять новые шарды по мере роста объема данных и нагрузки, что делает систему более масштабируемой.

- Увеличение производительности: Распределение данных по нескольким серверам позволяет обрабатывать больше запросов одновременно, что повышает общую производительность системы.

- Отказоустойчивость: Шарды могут быть настроены как реплика-сеты, что обеспечивает высокую доступность и защиту данных.

### Недостатки шардирования
- Сложность настройки: Шардирование требует более сложной конфигурации и управления по сравнению с обычными экземплярами MongoDB.

- Проблемы с выбором ключа шардирования: Неправильный выбор ключа шардирования может привести к неравномерному распределению данных и перегрузке некоторых шардов.

- Увеличение времени выполнения запросов: Запросы, которые не содержат ключа шардирования, могут потребовать обращения ко всем шардам, что увеличивает время выполнения.



## 3. Правильно выключать СУБД монго ( не рекомендуется через systemctl ее гасить, иногда не поднимается)  

```sh
use admin
db.shutdownServer()
```


## 4. Добавление новой ноды в реплика сет. 

[ссылка на документацию в Confluence](https://confluence.crpt.ru/pages/viewpage.action?pageId=169260630)

- ### Шаг 1: Подготовка нового сервера

Настройте конфигурацию в /etc/mongod.conf:
```yml
replication:
  oplogSizeMB: 1000000
  replSetName: RS0
```  

Запустите MongoDB:
```sh
sudo systemctl start mongod
sudo systemctl status mongod
```


- ### Шаг 2: Добавление сервера в реплика-сет
Подключитесь к основному серверу:
```sh
mongo --host <primary_server_host>:<port>
```

Добавьте новый сервер:
```js
rs.add({ host: "ServerNew:27017", priority: 0, votes: 0, hidden: true });
```

Дождитесь разбора лага репликации. Используйте команду:
```js
rs.printSecondaryReplicationInfo()
```

- ### Шаг 3: Настройка параметров нового узла
Измените параметры для нового узла на Primary:
id ноды смотрим в rs.conf().
Замените index_new_node на индекс новой ноды
```js
cfg = rs.conf();
cfg.members[<index_new_node>].priority = 1;  // Установите приоритет
cfg.members[<index_new_node>].votes = 1;      // Установите голоса
cfg.members[<index_new_node>].hidden = false; // Установите видимость
rs.reconfig(cfg);
```




## 5. Понимание и настройка приоритетов и голосовалок.  

[ссылка на документацию в Confluence](https://confluence.crpt.ru/display/TechAdm/priority+and+votes)  


**Приоритеты (Priority)**
Приоритет определяет, насколько вероятно, что узел станет основным (primary) в случае выборов. Узлы с более высоким приоритетом имеют больше шансов быть выбранными в качестве основного узла.   
Приоритеты могут быть установлены в диапазоне от 0 до 100, где:

- 0: Узел не может стать основным. Это полезно для узлов, которые вы хотите использовать только как вторичные (secondary) и не хотите, чтобы они участвовали в выборах.
- 1-100: Узел может стать основным, и его вероятность быть выбранным пропорциональна его приоритету. Узел с приоритетом 100 будет иметь больше шансов стать основным, чем узел с приоритетом 1.  

**Голосование (Votes)**
Голосование определяет, будет ли узел участвовать в выборах. Каждый узел в реплика-сете имеет голос, и общее количество голосов определяет, сколько голосов необходимо для выбора нового основного узла. 

### Правильное понимание голосования в MongoDB

- 1: Узел участвует в выборах и имеет один голос. Это стандартное поведение для всех узлов, которые могут стать основными (Primary).
- 0: Узел не участвует в выборах и не имеет голоса. Это поведение характерно для узлов, которые вы хотите использовать только как резервные, но не хотите, чтобы они влияли на выборы. Например, это может быть полезно для узлов, которые не должны быть основными, но могут хранить данные.
- Арбитры (Arbiters): Арбитры всегда имеют 1 голос и участвуют в выборах. Они не хранят данные, но их основная функция — обеспечить четное количество голосов в процессе выборов. Это позволяет избежать ситуации, когда два узла могут стать основными одновременно.


### Настройка приоритетов и голосования:

Подключитесь к основному узлу:
```sh
mongo --host <primary_server_host>:<port>
```

Получите текущую конфигурацию реплика-сета:
```js
cfg = rs.conf();
```
Измените параметры для нужного узла:
Например, если вы хотите изменить приоритет и голосование для узла с индексом 2:
```js
cfg.members[2].priority = 2;  // Установите приоритет
cfg.members[2].votes = 1;      // Установите голоса
```

Примените изменения:
```js
rs.reconfig(cfg);
```

### Примеры использования

**Настройка узла как резервного:**  
Если у вас есть узел, который вы хотите использовать только как резервный, вы можете установить его приоритет в 0 и голоса в 0:
```js
cfg.members[3].priority = 0;  // Не может стать основным
cfg.members[3].votes = 0;      // Не участвует в выборах
rs.reconfig(cfg);
```

**Настройка узла с высоким приоритетом:**  
Если у вас есть узел, который должен быть основным, вы можете установить его приоритет выше, чем у других узлов:
```js
cfg.members[1].priority = 10;  // Высокий приоритет
rs.reconfig(cfg);
```


## 6. Понимание и настройка tags set.  

[ссылка на документацию в Confluence](https://confluence.crpt.ru/display/TechAdm/tags+set)

**Теги (Tag Sets)** в MongoDB — это механизм, который позволяет управлять распределением данных в реплика-сетах, обеспечивая более гибкое управление репликацией и доступностью данных.   
Теги позволяют назначать определенные метки (теги) узлам в реплика-сете и использовать эти метки для управления тем, как данные реплицируются и где они могут быть доступны.

### Основные понятия:

- **Тег (Tag):** - это метка, которая назначается узлу в реплика-сете. Теги могут представлять различные характеристики, такие как географическое расположение, тип оборудования или уровень доступности.

- **Тег-сет (Tag Set):** - это набор тегов, который может быть назначен для определенной коллекции. Тег-сет определяет, какие узлы могут содержать данные для этой коллекции.

- **Тег-коллекция (Tagged Collection):** - это коллекция, которая использует тег-сет для управления тем, на каких узлах хранятся ее данные.

### Преимущества использования тегов

- Геораспределенные данные: Теги позволяют распределять данные по узлам в зависимости от их географического расположения, что может улучшить производительность и доступность. Если у вас есть приложение, которое обслуживает пользователей из разных регионов, вы можете использовать теги для хранения данных пользователей в соответствующих географических узлах, что может снизить задержку доступа.

- Управление доступностью: Вы можете настроить, чтобы определенные данные хранились только на узлах с определенными характеристиками, что может быть полезно для соблюдения требований к безопасности или доступности.

- Оптимизация нагрузки: Теги могут помочь в распределении нагрузки между узлами, позволяя более эффективно использовать ресурсы.

### Настройка тегов в MongoDB

### Шаг 1: Назначение тегов узлам
Подключитесь к основному узлу:

```sh
mongo --host <primary_server_host>:<port>
```

Получите текущую конфигурацию реплика-сета:
```js
cfg = rs.conf();
```

Назначьте теги узлам. Например, если у вас есть два узла, и вы хотите назначить им теги:
```js
cfg.members[0].tags = { location: "ru-east" };
cfg.members[1].tags = { location: "ru-west" };
```

Примените изменения:
```js
rs.reconfig(cfg);
```

### Шаг 2: Создание тег-сета для коллекции
Например, если вы хотите создать тег-сет для коллекции, который будет использовать теги, назначенные узлам:

```js
db.runCommand({
  shardCollection: "mydb.mycollection",
  key: { shardKey: 1 },
  tags: [
    { min: { shardKey: 1 }, max: { shardKey: 10 }, tag: "ru-east" },
    { min: { shardKey: 11 }, max: { shardKey: 20 }, tag: "ru-west" }
  ]
});
```
В этом примере данные с shardKey от 1 до 10 будут храниться на узлах с тегом ru-east, а данные с shardKey от 11 до 20 — на узлах с тегом ru-west.

### Шаг 3: Проверка конфигурации тегов
```js
rs.conf();
```




## 7. Умение плавно переключать праймари на секондари.

[ссылка на документацию в Confluence](https://confluence.crpt.ru/pages/viewpage.action?pageId=212210933)


## 8. Умение подключать к кластеру новой ноды с ДАННЫМИ которые лежат на соскедней ноды, так сказать уметь копировать данные между нодами.

[ссылка на документацию в Confluence](https://confluence.crpt.ru/pages/viewpage.action?pageId=212208986)


---

## Установка и настройка mongo


1. Установите ключ репозитория MongoDB:

```sh
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 656408E390CFB1F5
```

2. Добавьте репозиторий MongoDB APT в файл `/etc/apt/sources.list.d/mongodb.list`:

Ubuntu 20.04 LTS:
```sh
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```


3. Обновите список пакетов и установите MongoDB:

```sh
sudo apt update
sudo apt install mongodb-org
```

4. Если нужно установить определённую версию MongoDB, укажите её номер:

```sh
sudo apt install mongodb-org=<версия> mongodb-org-server=<версия> mongodb-org-shell=<версия> mongodb-org-mongos=<версия> mongodb-org-tools=<версия>
```

5. После установки MongoDB запустится автоматически. 

```sh
sudo systemctl enable mongod.service
sudo systemctl start mongod.service
```

6. Чтобы проверить установку и версию MongoDB, используйте команду:

```sh
mongod --version
```


## Создание primary и secondary в Replica Set


Создание основного (Primary) и вторичных (Secondary) узлов в реплика-сете MongoDB — это процесс, который позволяет обеспечить высокую доступность и отказоустойчивость данных. Ниже приведены шаги по созданию реплика-сета с основным и вторичными узлами.

### Шаг 1: Установка MongoDB
Убедитесь, что MongoDB установлен на всех узлах, которые вы хотите включить в реплика-сет (например, на 10.1.1.1, 10.1.1.2, 10.1.1.3):

```sh
mongod --version
```

### Шаг 2: Настройка конфигурации MongoDB
На каждом узле откройте файл конфигурации MongoDB, обычно находящийся по пути /etc/mongod.conf, и добавьте или измените следующие параметры:

```yml
replication:
  replSetName: "myReplicaSet"  # Укажите имя вашего реплика-сета
```

### Шаг 3: Запуск MongoDB
Запустите MongoDB на каждом узле:

```sh
sudo systemctl start mongod
```
### Шаг 4: Инициализация реплика-сета
Подключитесь к одному из узлов (например, к 10.1.1.1):
```sh
mongo --host 10.1.1.1
```

Инициализируйте реплика-сет:
```js
rs.initiate({
  _id: "myReplicaSet",  // Имя вашего реплика-сета
  members: [
    { _id: 0, host: "10.1.1.1:27017" },  // Основной узел
    { _id: 1, host: "10.1.1.2:27017" },  // Вторичный узел
    { _id: 2, host: "10.1.1.3:27017" }   // Вторичный узел
  ]
});
```

### Шаг 5: Проверка состояния реплика-сета
После инициализации реплика-сета вы можете проверить его состояние:

```js
rs.status();
```

### Шаг 6: Настройка приоритетов и голосования (опционально)
Если вы хотите настроить приоритеты и голосование для узлов, вы можете изменить конфигурацию реплика-сета. Например, чтобы установить приоритет для основного узла и вторичных узлов:

```js
cfg = rs.conf();
cfg.members[0].priority = 2;  // Основной узел
cfg.members[1].priority = 1;  // Вторичный узел
cfg.members[2].priority = 1;  // Вторичный узел
rs.reconfig(cfg);
```

### Шаг 7: Проверка работы реплика-сета
Теперь вы можете выполнять операции записи на основном узле и проверять, что данные реплицируются на вторичные узлы. Например, создайте базу данных и коллекцию:

```js
use myDatabase;
db.myCollection.insert({ name: "Test Document" });
```
Затем проверьте, что данные доступны на вторичных узлах:

Подключитесь к вторичному узлу:
```bash
mongo --host 10.1.1.2
```

Проверьте данные:
```js
use myDatabase;
db.myCollection.find();
```

## Подключение в Replica Set регулятора arbiter

### Шаг 1: Установка MongoDB
Убедитесь, что MongoDB установлен на сервере, который будет использоваться в качестве арбитра. Установите MongoDB, если он еще не установлен:

```sh
sudo apt update
sudo apt install -y mongodb
```
### Шаг 2: Настройка конфигурации MongoDB
На сервере, который будет использоваться в качестве арбитра, откройте файл конфигурации MongoDB (обычно /etc/mongod.conf) и добавьте или измените следующие параметры:

```yml
replication:
  replSetName: "myReplicaSet"  # Укажите имя вашего реплика-сета
```

### Шаг 3: Запуск MongoDB
Запустите MongoDB на сервере арбитра:

```sh
sudo systemctl start mongod
```

### Шаг 4: Добавление арбитра в реплика-сет
Подключитесь к основному узлу вашего реплика-сета:

```sh
mongo --host <primary_host>:<port>
```

Добавьте арбитра в реплика-сет. Используйте команду rs.addArb() для добавления арбитра. Например, если ваш арбитр находится на 10.1.1.4:

```js
rs.addArb("10.1.1.4:27017");
```

### Шаг 5: Проверка состояния реплика-сета

```js
rs.status();
```
В выводе вы должны увидеть арбитра с состоянием ARBITER.

Пример полной конфигурации

```ini
Основной узел: 10.1.1.1
Вторичный узел: 10.1.1.2
Вторичный узел: 10.1.1.3
Арбитр: 10.1.1.4
```
Инициализация реплика-сета
На основном узле выполните:

```js
rs.initiate({
  _id: "myReplicaSet",
  members: [
    { _id: 0, host: "10.1.1.1:27017" },  // Основной узел
    { _id: 1, host: "10.1.1.2:27017" },  // Вторичный узел
    { _id: 2, host: "10.1.1.3:27017" },  // Вторичный узел
    { _id: 3, host: "10.1.1.4:27017", arbiterOnly: true }  // Арбитр
  ]
});
```


## Initial Sync
[ссылка на документацию Confluence](https://confluence.crpt.ru/pages/viewpage.action?pageId=169250057)
 
**Initial sync** — это процесс, который происходит, когда новый вторичный узел (или узел, который был временно отключен) добавляется в реплика-сет.   **Основная цель** — обеспечить, чтобы новый узел имел полную и актуальную копию данных с основного узла. Во время этого процесса новый узел выполняет следующие действия:

- **Копирование данных:** Вторичный узел делает начальный снимок данных с основного узла, копируя все базы данных, коллекции и индексы. Это включает в себя все данные, которые находятся на основном узле на момент начала процесса.

- **Синхронизация:** После копирования данных новый узел начинает синхронизироваться с основным узлом, применяя все операции, которые произошли после завершения начального копирования. Это позволяет вторичному узлу быть в актуальном состоянии относительно основного узла.

- **Фрагментация:** Хотя initial sync сам по себе не предназначен для устранения фрагментации, он может помочь в создании более упорядоченной структуры данных на новом узле. Однако основная цель этого процесса — обеспечить, чтобы новый узел имел полную и актуальную копию данных.

*Уточнение*  
Initial sync не является процессом, который используется для регулярного сжатия или дефрагментации данных. Эти процессы могут происходить в рамках других механизмов, таких как компакшен (compaction) и управление SSTable в других системах, но не являются частью процесса initial sync в MongoDB.

