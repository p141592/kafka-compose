# Kafka Compose

Локальное окружение для разработки с Kafka, Redpanda Console и PostgreSQL.

## Состав

| Сервис | Образ | Назначение | Доступ с хоста |
| --- | --- | --- | --- |
| `kafka` | `apache/kafka:4.3.0` | Одноузловой Kafka-брокер в KRaft-режиме | `localhost:9092` |
| `console` | `docker.redpanda.com/redpandadata/console:v2.8.15` | Веб-интерфейс для просмотра топиков, сообщений и consumer groups | http://localhost:8080 |
| `db` | `postgres:18.4` | PostgreSQL для локальных интеграций | `localhost:5432` |

## Требования

- Docker
- Docker Compose v2 (`docker compose`)

## Быстрый старт

Запустить окружение:

```bash
docker compose up -d
```

Открыть Redpanda Console:

```text
http://localhost:8080
```

Подключение к Kafka:

- с хоста: `localhost:9092`
- из контейнеров в этой compose-сети: `kafka:29092`

Подключение к PostgreSQL:

```text
host: localhost
port: 5432
database: test_db
user: postgres
password: postgres
```

## Проверка Kafka

Показать список топиков:

```bash
docker compose exec kafka /opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --list
```

Создать тестовый топик:

```bash
docker compose exec kafka /opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --create \
  --topic demo-events \
  --partitions 1 \
  --replication-factor 1
```

Отправить сообщение:

```bash
docker compose exec kafka /opt/kafka/bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic demo-events
```

После запуска producer введите сообщение и нажмите `Enter`.

Прочитать сообщения:

```bash
docker compose exec kafka /opt/kafka/bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic demo-events \
  --from-beginning
```

## Управление окружением

Посмотреть логи:

```bash
docker compose logs -f
```

Остановить контейнеры:

```bash
docker compose down
```

Остановить и удалить локальные данные контейнеров:

```bash
docker compose down -v
```

## Конфигурация

Redpanda Console читает конфигурацию из [`console_config/config.yaml`](console_config/config.yaml). Сейчас она подключается к Kafka по внутреннему адресу compose-сети:

```yaml
kafka:
  brokers:
    - kafka:29092
```

Kafka работает без ZooKeeper, в одноузловом KRaft-режиме. Настройки listener'ов находятся в [`docker-compose.yaml`](docker-compose.yaml).
