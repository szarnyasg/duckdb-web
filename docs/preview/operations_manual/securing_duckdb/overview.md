---
layout: docu
title: Securing DuckDB
---

DuckDB is quite powerful, which can be problematic, especially if untrusted SQL queries are run, e.g., from public-facing user inputs.
This page lists some options to restrict the potential fallout from malicious SQL queries.

The approach to securing DuckDB varies depending on your use case, environment, and potential attack models.
Therefore, consider the security-related configuration options carefully, especially when working with confidential datasets.

If you plan to embed DuckDB in your application, please consult the [“Embedding DuckDB”]({% link docs/preview/operations_manual/securing_duckdb/embedding_duckdb.md %}) page.

## Reporting Vulnerabilities

If you discover a potential vulnerability, please [report it confidentially via GitHub](https://github.com/duckdb/duckdb/security/advisories/new).

## Safe Mode (CLI)

DuckDB's CLI client supports [“safe mode”]({% link docs/preview/clients/cli/safe_mode.md %}), which prevents DuckDB from accessing external files other than the database file.
This can be activated via a command line argument or a [dot command]({% link docs/preview/clients/cli/dot_commands.md %}):

```batch
duckdb -safe ...
```

```plsql
.safe_mode
```

## Restricting File Access

DuckDB can list directories and read arbitrary files via its CSV parser’s [`read_csv` function]({% link docs/preview/data/csv/overview.md %}) or read text via the [`read_text` function]({% link docs/preview/sql/functions/text.md %}#read_textsource).
This makes it possible to read from the local file system, for example:

```sql
SELECT *
FROM read_csv('/etc/passwd', sep = ':');
```

### Disabling File Access

Files access can be disabled in two ways. First, you can disable individual file systems. For example:

```sql
SET disabled_filesystems = 'LocalFileSystem';
```

Second, you can also completely disable external access by setting the [`enable_external_access` option]({% link docs/preview/configuration/overview.md %}#configuration-reference) option to `false`.

```sql
SET enable_external_access = false;
```

This setting implies that:

* `ATTACH` cannot attach to a database in a file.
* `COPY` cannot read to or write from files.
* Functions such as `read_csv`, `read_parquet`, `read_json`, etc. cannot read from an external source.

### The `allowed_directories` and `allowed_paths` Options

You can restrict DuckDB's access to certain directories or files using the `allowed_directories` and `allowed_paths` options (respectively).
These options allows fine-grained access control for the file system.
For example, you can set DuckDB to only use the `/tmp` directory.

```sql
SET allowed_directories = ['/tmp'];  
SET enable_external_access = false;  
FROM read_csv('test.csv');  
```

With the setting applied, DuckDB will refuse to read files in the current working directory:

```console
Permission Error:
Cannot access file "test.csv" - file system operations are disabled by configuration  
```

## Secrets

[Secrets]({% link docs/preview/configuration/secrets_manager.md %}) are used to manage credentials to log into third party services like AWS or Azure. DuckDB can show a list of secrets using the `duckdb_secrets()` table function. This will redact any sensitive information such as security keys by default. The `allow_unredacted_secrets` option can be set to show all information contained within a security key. It is recommended not to turn on this option if you are running untrusted SQL input.

Queries can access the secrets defined in the Secrets Manager. For example, if there is a secret defined to authenticate with a user, who has write privileges to a given AWS S3 bucket, queries may write to that bucket. This is applicable for both persistent and temporary secrets.

[Persistent secrets]({% link docs/preview/configuration/secrets_manager.md %}#persistent-secrets) are stored in unencrypted binary format on the disk. These have the same permissions as SSH keys, `600`, i.e., only user who is running the DuckDB (parent) process can read and write them.

## Locking Configurations

Security-related configuration settings generally lock themselves for safety reasons. For example, while we can disable [community extensions]({% link community_extensions/index.md %}) using the `SET allow_community_extensions = false`, we cannot re-enable them again after the fact without restarting the database. Trying to do so will result in an error:

```console
Invalid Input Error: Cannot upgrade allow_community_extensions setting while database is running
```

This prevents untrusted SQL input from re-enabling settings that were explicitly disabled for security reasons.

Nevertheless, many configuration settings do not disable themselves, such as the resource constraints. If you allow users to run SQL statements unrestricted on your own hardware, it is recommended that you lock the configuration after your own configuration has finished using the following command:

```sql
SET lock_configuration = true;
```

This prevents any configuration settings from being modified from that point onwards.

## Prepared Statements to Prevent SQL Injection

Similarly to other SQL databases, it's recommended to use [prepared statements]({% link docs/preview/sql/query_syntax/prepared_statements.md %}) in DuckDB to prevent [SQL injection](https://en.wikipedia.org/wiki/SQL_injection).

**Therefore, avoid concatenating strings for queries:**

```python
import duckdb
duckdb.execute("SELECT * FROM (VALUES (32, 'a'), (42, 'b')) t(x) WHERE x = " + str(42)).fetchall()
```

**Instead, use prepared statements:**

```python
import duckdb
duckdb.execute("SELECT * FROM (VALUES (32, 'a'), (42, 'b')) t(x) WHERE x = ?", [42]).fetchall()
```

## Constrain Resource Usage

DuckDB can use quite a lot of CPU, RAM, and disk space. To avoid denial of service attacks, these resources can be limited.

The number of CPU threads that DuckDB can use can be set using, for example:

```sql
SET threads = 4;
```

Where 4 is the number of allowed threads.

The maximum amount of memory (RAM) can also be limited, for example:

```sql
SET memory_limit = '4GB';
```

The size of the temporary file directory can be limited with:

```sql
SET max_temp_directory_size = '4GB';
```

## Extensions

DuckDB has a powerful extension mechanism, which have the same privileges as the user running DuckDB's (parent) process.
This introduces security considerations. Therefore, we recommend reviewing the configuration options for [securing extensions]({% link docs/preview/operations_manual/securing_duckdb/securing_extensions.md %}).

## Privileges

Avoid running DuckDB as a root user (e.g., using `sudo`).
There is no good reason to run DuckDB as root.

## Generic Solutions

Securing DuckDB can also be supported via proven means, for example:

* Scoping user privileges via [`chroot`](https://en.wikipedia.org/wiki/Chroot), relying on the operating system
* Containerization, e.g., via Docker or Podman. See the [“DuckDB Docker Container” page]({% link docs/preview/operations_manual/duckdb_docker.md %})
* Running DuckDB in WebAssembly
