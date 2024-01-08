## Elasticsearch

### Ключи запуска:


```YML
image: registry.dev.esbo.adc.spb:5001/elasticsearch:6.5.4 # определяет docker-образ, который будет использоваться для создания контейнера ES
restart: 
  unless-stopped
container_name: 
  elasticsearch
hostname: 
  elasticsearch
environment:
    - "ES_JAVA_OPTS=-Xms1024m -Xmx1024m -XX:UseAVX=1" # настройка JVM, выделенная память
    - bootstrap.memory_lock=false
    - cluster.name=esbo_es_service # устанавливает имя кластера ES
    - discovery.type=single-node # указывает, что ES будет работать в режиме одного узла
    - xpack.security.enabled=false # настройки XPACK (можно прописывать в elasticsearch.yml)
    - xpack.monitoring.enabled=false
networks:
  petrograd:
ports:
    - 9300:9300 # Elasticsearch HTTP, через этот порт можно отправлять запросы к ES по http
    - 9200:9200 # Elasticsearch TCP транспорт
labels:
    NAME: "elasticsearch"
volumes:
    - /data/elasticsearch/data:/usr/share/elasticsearch/data #хранилище данных ES
```

### Разворачиваение ES
См. [Разворачивание Elasticsearch на VDS-сервере](ES_results/README.md)


### Вариант настройки Elasticsearch при переносе данных с одного хоста на другой: 


#### Способ 1. Snapshot/Restore

См. [Официальная инструкция](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html)
1) Развернуть кластер на двух серверах (текущий и новый)
2) После отключить старый кластер


#### Способ 2. Downtime

1) Остановить службу Elasticsearch
2) Поставить на новом хосте ту же версию ES
3) Перенести целиком все каталоги, тома, где хранятся данные `/var/lib/elasticsearch` и конфигов `/etc/elasticsearch`, возможны другие пути
