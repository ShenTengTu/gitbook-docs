# 25.1. SQL Dump

The idea behind this dump method is to generate a file with SQL commands that, when fed back to the server, will recreate the database in the same state as it was at the time of the dump. PostgreSQL provides the utility program [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) for this purpose. The basic usage of this command is:

```text
pg_dump dbname > dumpfile
```

As you see, pg\_dump writes its result to the standard output. We will see below how this can be useful. While the above command creates a text file, pg\_dump can create files in other formats that allow for parallelism and more fine-grained control of object restoration.

pg\_dump is a regular PostgreSQL client application \(albeit a particularly clever one\). This means that you can perform this backup procedure from any remote host that has access to the database. But remember that pg\_dump does not operate with special permissions. In particular, it must have read access to all tables that you want to back up, so in order to back up the entire database you almost always have to run it as a database superuser. \(If you do not have sufficient privileges to back up the entire database, you can still back up portions of the database to which you do have access using options such as `-n` _`schema`_ or `-t` _`table`_.\)

To specify which database server pg\_dump should contact, use the command line options `-h` _`host`_ and `-p` _`port`_. The default host is the local host or whatever your `PGHOST` environment variable specifies. Similarly, the default port is indicated by the `PGPORT` environment variable or, failing that, by the compiled-in default. \(Conveniently, the server will normally have the same compiled-in default.\)

Like any other PostgreSQL client application, pg\_dump will by default connect with the database user name that is equal to the current operating system user name. To override this, either specify the `-U` option or set the environment variable `PGUSER`. Remember that pg\_dump connections are subject to the normal client authentication mechanisms \(which are described in [Chapter 20](https://www.postgresql.org/docs/current/client-authentication.html)\).

An important advantage of pg\_dump over the other backup methods described later is that pg\_dump's output can generally be re-loaded into newer versions of PostgreSQL, whereas file-level backups and continuous archiving are both extremely server-version-specific. pg\_dump is also the only method that will work when transferring a database to a different machine architecture, such as going from a 32-bit to a 64-bit server.

Dumps created by pg\_dump are internally consistent, meaning, the dump represents a snapshot of the database at the time pg\_dump began running. pg\_dump does not block other operations on the database while it is working. \(Exceptions are those operations that need to operate with an exclusive lock, such as most forms of `ALTER TABLE`.\)

## 25.1.1. Restoring the Dump

Text files created by pg\_dump are intended to be read in by the psql program. The general command form to restore a dump is

```text
psql dbname < dumpfile
```

where _`dumpfile`_ is the file output by the pg\_dump command. The database _`dbname`_ will not be created by this command, so you must create it yourself from `template0` before executing psql \(e.g., with `createdb -T template0` _`dbname`_\). psql supports options similar to pg\_dump for specifying the database server to connect to and the user name to use. See the [psql](https://www.postgresql.org/docs/current/app-psql.html) reference page for more information. Non-text file dumps are restored using the [pg\_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) utility.

Before restoring an SQL dump, all the users who own objects or were granted permissions on objects in the dumped database must already exist. If they do not, the restore will fail to recreate the objects with the original ownership and/or permissions. \(Sometimes this is what you want, but usually it is not.\)

By default, the psql script will continue to execute after an SQL error is encountered. You might wish to run psql with the `ON_ERROR_STOP` variable set to alter that behavior and have psql exit with an exit status of 3 if an SQL error occurs:

```text
psql --set ON_ERROR_STOP=on dbname < dumpfile
```

Either way, you will only have a partially restored database. Alternatively, you can specify that the whole dump should be restored as a single transaction, so the restore is either fully completed or fully rolled back. This mode can be specified by passing the `-1` or `--single-transaction` command-line options to psql. When using this mode, be aware that even a minor error can rollback a restore that has already run for many hours. However, that might still be preferable to manually cleaning up a complex database after a partially restored dump.

The ability of pg\_dump and psql to write to or read from pipes makes it possible to dump a database directly from one server to another, for example:

```text
pg_dump -h host1 dbname | psql -h host2 dbname
```

#### Important

The dumps produced by pg\_dump are relative to `template0`. This means that any languages, procedures, etc. added via `template1` will also be dumped by pg\_dump. As a result, when restoring, if you are using a customized `template1`, you must create the empty database from `template0`, as in the example above.

After restoring a backup, it is wise to run [ANALYZE](https://www.postgresql.org/docs/current/sql-analyze.html) on each database so the query optimizer has useful statistics; see [Section 24.1.3](https://www.postgresql.org/docs/current/routine-vacuuming.html#VACUUM-FOR-STATISTICS) and [Section 24.1.6](https://www.postgresql.org/docs/current/routine-vacuuming.html#AUTOVACUUM) for more information. For more advice on how to load large amounts of data into PostgreSQL efficiently, refer to [Section 14.4](https://www.postgresql.org/docs/current/populate.html).

## 25.1.2. Using pg\_dumpall

pg\_dump dumps only a single database at a time, and it does not dump information about roles or tablespaces \(because those are cluster-wide rather than per-database\). To support convenient dumping of the entire contents of a database cluster, the [pg\_dumpall](https://www.postgresql.org/docs/current/app-pg-dumpall.html) program is provided. pg\_dumpall backs up each database in a given cluster, and also preserves cluster-wide data such as role and tablespace definitions. The basic usage of this command is:

```text
pg_dumpall > dumpfile
```

The resulting dump can be restored with psql:

```text
psql -f dumpfile postgres
```

\(Actually, you can specify any existing database name to start from, but if you are loading into an empty cluster then `postgres` should usually be used.\) It is always necessary to have database superuser access when restoring a pg\_dumpall dump, as that is required to restore the role and tablespace information. If you use tablespaces, make sure that the tablespace paths in the dump are appropriate for the new installation.

pg\_dumpall works by emitting commands to re-create roles, tablespaces, and empty databases, then invoking pg\_dump for each database. This means that while each database will be internally consistent, the snapshots of different databases are not synchronized.

Cluster-wide data can be dumped alone using the pg\_dumpall `--globals-only` option. This is necessary to fully backup the cluster if running the pg\_dump command on individual databases.

## 25.1.3. Handling Large Databases

Some operating systems have maximum file size limits that cause problems when creating large pg\_dump output files. Fortunately, pg\_dump can write to the standard output, so you can use standard Unix tools to work around this potential problem. There are several possible methods:

**Use compressed dumps.**  You can use your favorite compression program, for example gzip:

```text
pg_dump dbname | gzip > filename.gz
```

Reload with:

```text
gunzip -c filename.gz | psql dbname
```

or:

```text
cat filename.gz | gunzip | psql dbname
```

**Use `split`.**  The `split` command allows you to split the output into smaller files that are acceptable in size to the underlying file system. For example, to make chunks of 1 megabyte:

```text
pg_dump dbname | split -b 1m - filename
```

Reload with:

```text
cat filename* | psql dbname
```

**Use pg\_dump's custom dump format.**  If PostgreSQL was built on a system with the zlib compression library installed, the custom dump format will compress data as it writes it to the output file. This will produce dump file sizes similar to using `gzip`, but it has the added advantage that tables can be restored selectively. The following command dumps a database using the custom dump format:

```text
pg_dump -Fc dbname > filename
```

A custom-format dump is not a script for psql, but instead must be restored with pg\_restore, for example:

```text
pg_restore -d dbname filename
```

See the [pg\_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and [pg\_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) reference pages for details.

For very large databases, you might need to combine `split` with one of the other two approaches.

**Use pg\_dump's parallel dump feature.**  To speed up the dump of a large database, you can use pg\_dump's parallel mode. This will dump multiple tables at the same time. You can control the degree of parallelism with the `-j` parameter. Parallel dumps are only supported for the "directory" archive format.

```text
pg_dump -j num -F d -f out.dir dbname
```

You can use `pg_restore -j` to restore a dump in parallel. This will work for any archive of either the "custom" or the "directory" archive mode, whether or not it has been created with `pg_dump -j`.

