# Запуск {{ ydb-short-name }} в Docker

Для отладки или тестирования вы можете запустить [Docker](https://docs.docker.com/get-docker/)-контейнер YDB.

## Параметры соединения {#conn}

В результате выполнения описанных ниже инструкций вы получите локальную базу данных YDB, к которой можно будет обратиться по следующим реквизитам:

{% list tabs %}

- gRPC

  - [Эндпоинт](../../../concepts/connect.md#endpoint): `grpc://localhost:2136`
  - [Путь базы данных](../../../concepts/connect.md#database): `/local`
  - [Аутентификация](../../../concepts/auth.md): Анонимная (без аутентификации)

- gRPCs/TLS

  - [Эндпоинт](../../../concepts/connect.md#endpoint): `grpcs://localhost:2135`
  - [Путь базы данных](../../../concepts/connect.md#database): `/local`
  - [Аутентификация](../../../concepts/auth.md): Анонимная (без аутентификации)

{% endlist %}

## Установка {#install}

Загрузите актуальную публичную версию Docker-образа:

```bash
docker pull {{ ydb_local_docker_image }}:{{ ydb_local_docker_image_tag }}
```

Проверьте, что Docker-образ успешно выгружен:

```bash
docker image list | grep {{ ydb_local_docker_image }}
```

Результат выполнения:

```bash
{{ ydb_local_docker_image }}           {{ ydb_local_docker_image_tag }}   b73c5c1441af   2 months ago   793MB
```

## Запуск {#start}

Docker-контейнер YDB использует ресурсы хост-системы (CPU, RAM) в пределах выделенных настройками Docker.

Docker-контейнер YDB хранит данные в файловой системе контейнера, разделы которой отражаются на директории в хост-системе. Приведенная ниже команда запуска контейнера создаст файлы в текущей директории, поэтому перед запуском создайте рабочую директорию, и выполняйте запуск из неё:

```bash
docker run -d --rm --name ydb-local -h localhost \
  -p 2135:2135 -p 8765:8765 -p 2136:2136 \
  -v $(pwd)/ydb_certs:/ydb_certs -v $(pwd)/ydb_data:/ydb_data \
  -e YDB_DEFAULT_LOG_LEVEL=NOTICE \
  -e GRPC_TLS_PORT=2135 -e GRPC_PORT=2136 -e MON_PORT=8765 \
  {{ ydb_local_docker_image}}:{{ ydb_local_docker_image_tag }}
```

{% list tabs %}

- Хранение данных на диске

    ```bash
    docker run -d --rm --name ydb-local -h localhost \
      -p 2135:2135 -p 8765:8765 -p 2136:2136 \
      -v $(pwd)/ydb_certs:/ydb_certs -v $(pwd)/ydb_data:/ydb_data \
      -e YDB_DEFAULT_LOG_LEVEL=NOTICE \
      -e GRPC_TLS_PORT=2135 -e GRPC_PORT=2136 -e MON_PORT=8765 \
      {{ ydb_local_docker_image}}:{{ ydb_local_docker_image_tag }}
    ```
    
    {% note warning %}

    На данный момент хранение данных на диске не поддерживается на Apple Silicon (M1 or M2). Используйте команду с вкладки "Хранение данных в памяти", если хотите попробовать {{ ydb-short-name }} на данных процессорах.

    {% endnote %}

- Хранение данных в памяти
    ```bash
    docker run -d --rm --name ydb-local -h localhost \
      -p 2135:2135 -p 8765:8765 -p 2136:2136 \
      -v $(pwd)/ydb_certs:/ydb_certs -v $(pwd)/ydb_data:/ydb_data \
      -e YDB_DEFAULT_LOG_LEVEL=NOTICE \
      -e GRPC_TLS_PORT=2135 -e GRPC_PORT=2136 -e MON_PORT=8765 \
      -e YDB_USE_IN_MEMORY_PDISKS=true \
      {{ ydb_local_docker_image}}:{{ ydb_local_docker_image_tag }}
    ```
{% endlist %}

При успешном запуске будет выведен идентификатор созданного контейнера.
### Параметры запуска {#start-pars}
`-d`: Запустить Docker-контейнер в фоновом режиме.
`--rm`: Удалить контейнер после завершения его работы.
`--name`: Имя контейнера. Укажите `ydb-local`, чтобы приведенные ниже инструкции по остановке контейнера можно было выполнить копированием текста через буфер обмена.
`-h`: Имя хоста контейнера. Должно быть обязательно передано значение `localhost`, иначе контейнер будет запущен со случайным именем хоста.
`-v`: Монтировать директории хост-системы в контейнер в виде `<директория хост-системы>:<директория монтирования в контейнере>`. Контейнер YDB использует следующие директории монтирования:
- `/ydb_data`: Размещение данных. Если данная директория не смонтирована, то контейнер будет запущен без сохранения данных на диск хост-системы.
- `/ydb_certs`: Размещение сертификатов для TLS соединения. Запущенный контейнер запишет туда сертификаты, которые вам нужно использовать для клиентского подключения с использованием TLS. Если данная директория не смонтирована, то вы не сможете подключиться по TLS, так как не будете обладать информацией о сертификате.
`-e`: Задать переменные окружения в виде `<имя>=<значение>`. Контейнер YDB использует следующие переменные окружения:
- `YDB_DEFAULT_LOG_LEVEL`: Уровень логирования. Допустимые значения: `CRIT`, `ERROR`, `WARN`, `NOTICE`, `INFO`. По умолчанию `NOTICE`.
- `GRPC_PORT`: Порт для нешифрованных соединений. По умолчанию 2136.
- `GRPC_TLS_PORT`: Порт для соединений с использованием TLS. По умолчанию 2135.
- `MON_PORT`: Порт для встроенного web-ui со средствами [мониторинга и интроспекции](../../../maintenance/embedded_monitoring/ydb_monitoring.md). По умолчанию 8765.
- `YDB_PDISK_SIZE`: Размер диска для хранения данных в формате `<NUM>GB` (например, `YDB_PDISK_SIZE=128GB`). Допустимые значения: от `80GB` и выше. По умолчанию 80GB.
- `YDB_USE_IN_MEMORY_PDISKS`: Использование дисков в памяти. Допустимые значения `true`, `false`, по умолчанию `false`. Во включенном состоянии не использует файловую систему контейнера для работы с данными, все данные хранятся только в памяти процесса, и теряются при его остановке. В настоящее время запуск контейнера на процессоре Apple Silicon (M1 или M2) возможен только в этом режиме.
- `YDB_FEATURE_FLAGS`: Флаги, позволяющие включить функции, отключенные по-умолчанию. Используется для функций, находящихся в разработке (по-умолчанию они выключены). Перечисляются через запятую.
- `POSTGRES_USER` - создать пользователя с указанным логином, используется для подключения через postgres-протокол.
- `POSTGRES_PASSWORD` - задать пароль пользователя для подключения через postgres-протокол.
- `YDB_TABLE_ENABLE_PREPARED_DDL` - временная опция, нужна для запуска Postgres-слоя совместимости, в будущем будет удалена.
`-p`: Опубликовать порты контейнера на хост-системе. Все применяемые порты должны быть явно перечислены, даже если используются значения по умолчанию.

{% include [_includes/storage-device-requirements.md](../../../_includes/storage-device-requirements.md) %}

{% note info %}

Инициализация Docker-контейнера, в зависимости от выделенных ресурсов, может занять несколько минут. До окончания инициализации база данных будет недоступна.

{% endnote %}

## Выполнение запросов {#request}

[Установите](../../../reference/ydb-cli/install.md) YDB CLI и выполните запрос, например:

```bash
ydb -e grpc://localhost:2136 -d /local scheme ls
```

Для успешного соединения с использованием TLS в параметры соединения нужно добавить имя файла с сертификатом. Запрос в примере ниже должен быть выполнен из той же рабочей директории, которую вы использовали для запуска контейнера:

```bash
ydb -e grpcs://localhost:2135 --ca-file ydb_certs/ca.pem -d /local scheme ls
```

Предсобранная версия [YDB CLI](../../../reference/ydb-cli/index.md) также доступа внутри образа:

```bash
docker exec <container_id> /ydb -e grpc://localhost:2136 -d /local scheme ls
```

, где

`<container_id>`: идентификатор контейнера, выведенный при его [запуске](#start).


## Остановка {#stop}

По окончании работы остановите Docker-контейнер:

```bash
docker kill ydb-local
```

## Лицензия и используемые компоненты {#license}

В корне Docker-контейнера расположены файл с текстом лицензионного соглашения (`LICENSE`) и список используемых компонентов и их лицензии (`THIRD_PARTY_LICENSES`).

Просмотрите текст лицензионного соглашения:

```bash
docker run --rm -it --entrypoint cat {{ ydb_local_docker_image }} LICENSE
```

Просмотрите все использованные при создании компоненты и их лицензии:

```bash
docker run --rm -it --entrypoint cat {{ ydb_local_docker_image }} THIRD_PARTY_LICENSES
```