# The-test-item-for-The-Black-Silicon-Dioxide
The test item for The Black Silicon Dioxide - это пример выполнения тестового задания

* Cодержание проекта: 

|Имя директории|Описание директории|
|---|---|
|./dev|директория с файлами для сборки контейнеров PostgreSQL-BDR|
|./cfg|содержит конфигурационный файлы сервисов/контейнеров|
|./dat|директория под данные PostgreSQL|

* Тестировался на:
	* `Docker version 17.12.0-ce` + `Docker-compose version 1.8.0`
	* `Docker version 1.13.1` + `Docker-compose version 1.8.0`

**Примечание:** *Возможно для запуска окружения в вашей среде придется поправить файл `./dev/Dockerfile`, вызвано это разным поведением разных версий Docker (`docker-ce` и `dockre.io`) при обработке параметра FROM.
Сейчас адаптировано на работу с docker-ce.*
    

### PostgreSQL 
PostgreSQL - версия BDR-9.4.

### Consul
Consul запущен в качестве службы обнаружения сервисоа и DNS'а.
* Конфигурационный файл: `./cfg/server.json`
* Настройка согласно: https://www.consul.io/docs/agent/dns.html
* Запуск согласно: https://docs.docker.com/samples/library/consul/#running-consul-for-development

### Regictrator 
Regictrator читает из сокета Docker'а информацию о запущенных/остановленных контейнерах и передает информацию в Consul (`consul://172.20.0.2:8500`).
Переменные `SERVICE_NAME` и `SERVICE_TAGS` назначаются контейнерам для упрощения идентификации, что также отражается в Consul'е.
* Настройка и запуск согласно: https://github.com/gliderlabs/registrator
    
### HAProxy 
HAProxy прокcирует (балансирует) подключения к нодам PostgreSQL в режиме roundrobin.
* Конфигурационный файл: `./cfg/haproxy.cfg`

|Название листенера|Порт|Описание|
|--|--|--|
|psqlpool|15432|Проверка выполняется стандартным механизмом для проверки PostrgeSQL `pgsql-check`, по имени DNS. Балансировка выполняется между прошедшими проверку|
|psqlpooldns|25432|Проверка выполняется механизмом `resolvers` - проверка через DNS. Балансировка выполняется между хостами возвращенными DNS'ом. DNS'ом выступает Consul|
* Настройка согласно:
	* https://cbonte.github.io/haproxy-dconv/1.8/configuration.html#5.3
	* https://cbonte.github.io/haproxy-dconv/1.8/configuration.html#5.3.2

### Сеть
Сеть `connet` создана для того, чтобы можно было контролировать какой IP получит DNS (Consul).

### Запуск контейнеров
Очередность запуска сервисов (контейнеров):
1. Consul (`contest01`)
2.  Registrator (`regtest01`)
3. PostgrSQL (`pgtest001`, `pgtest002`, `pgtest003`)
4. HAProxy (`hapoxy001`)

### Дополнительно
В файле `PSQL_cfg.txt` - сведения по настройки multi-master репликации PostgrsSQL-BDR.

### Cхема проекта
![Scheme](https://raw.githubusercontent.com/esendjer/The-test-item-for-The-Black-Silicon-Dioxide/master/Scheme.png)
