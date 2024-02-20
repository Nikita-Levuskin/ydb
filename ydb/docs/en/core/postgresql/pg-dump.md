# Dump data form PostgreSQL

Data from PostgreSQL can be migrated to YDB using utilities such as [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html), [psql](https://www.postgresql.org/docs/current/app-psql.html), and [YDB CLI](../reference/ydb-cli/index.md). The [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and [psql](https://www.postgresql.org/docs/current/app-psql.html) utilities are installed with PostgreSQL. [YDB CLI](../reference/ydb-cli/index.md) is YDB's command-line client, which is [installed separately](../reference/ydb-cli/install.md).

To do this, you need to:

1. Create a data dump using [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) with the following parameters:
    * `--inserts` — to add data using the [INSERT](./statements/insert_into.md) command, instead of the [COPY](https://www.postgresql.org/docs/current/sql-copy.html) protocol.
    * `--column-inserts` — to add data using the [INSERT](./statements/insert_into.md) command with column names.
    * `--rows-per-insert=1000` — to insert data in batches to speed up the process.
    * `--encoding=utf_8` — YDB only supports string data in [UTF-8](https://en.wikipedia.org/wiki/UTF-8).
2. Convert the dump to a format supported by YDB using the `ydb tools pg-convert` command from [YDB CLI](../reference/ydb-cli/index.md).
3. Load the result into YDB in PostgreSQL compatibility mode.


## pg-convert command {#pg-convert}

The `ydb tools pg-convert` command reads a dump file or standard input created by the [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) utility, performs transformations, and outputs to standard output a dump that can be sent to YDB's PostgreSQL-compatible middleware.

`ydb tools pg-convert` performs the following transformations:

* Moving the creation of the primary key into the body of the [CREATE TABLE](./statements/create_table.md) command.
* Removing the `public` schema from table names.
* Deleting the `WITH (...)` section in `CREATE TABLE`.
* Commenting out unsupported constructs (optionally):
  * `SELECT pg_catalog.set_config.*`
  * `ALTER TABLE`

If the CLI cannot find a table's primary key, it will automatically create a [BIGSERIAL](https://www.postgresql.org/docs/current/datatype-numeric.html#DATATYPE-SERIAL) column named `__ydb_stub_id` as the primary key.

The general form of the command:

```bash
{{ ydb-cli }} [global options...] tools pg-convert [options...]
```

* `global options` — [global parameters](../reference/ydb-cli/commands/global-options.md).
* `options` — [subcommand parameters](#options).

### subcommand parameters {#options}

| Name                  | Description |
|-----------------------|-------------|
| `-i`                  | The name of the file containing the original dump. If the option is not specified, the dump is read from standard input. |
| `--ignore-unsupported`| When this option is specified, unsupported constructs will be commented out in the resulting dump and duplicated in standard error. By default, if unsupported constructs are detected, the command returns an error. This does not apply to `ALTER TABLE` expressions that define a table's primary key, as they are commented out in any case. |


{% note warning %}

When loading large dumps, reading from standard input is not recommended because the entire dump will be stored in RAM. It is advised to use the file option, in which case the CLI will only keep a small portion of the dump in memory.

{% endnote %}

## Example of importing a dump into YDB {#examples}

As an example, data generated by [pgbench](https://www.postgresql.org/docs/current/pgbench.html) will be loaded.

1. Start Docker containers with PostgreSQL and YDB:

    ```bash
    docker run -d --rm -e POSTGRES_USER=root -e POSTGRES_PASSWORD=1234 \
        -e POSTGRES_DB=local --name postgres postgres:14
    docker run --name ydb-postgres -d --pull always -p 5432:5432 -p 8765:8765 \
        -e POSTGRES_USER=root -e POSTGRES_PASSWORD=1234 \
        -e YDB_FEATURE_FLAGS=enable_temp_tables \
        -e YDB_TABLE_ENABLE_PREPARED_DDL=true \
        -e YDB_USE_IN_MEMORY_PDISKS=true \
        ghcr.io/ydb-platform/ydb-local:latest
    ```

2. Generate data through [pgbench](https://www.postgresql.org/docs/current/pgbench.html):

    ```bash
    docker exec postgres pgbench -i postgres://root:1234@localhost/local
    ```

3. Create a dump of the database using [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and load it into YDB:

    ```bash
    docker exec postgres pg_dump -U root --format=c --file=/var/lib/postgresql/data/dump.sql local
    docker cp postgres:/var/lib/postgresql/data/dump.sql .
    cat dump.sql | docker exec -i ydb-postgres psql -U root -d local
    ```