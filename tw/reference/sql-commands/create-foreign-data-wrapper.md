# CREATE FOREIGN DATA WRAPPER

CREATE FOREIGN DATA WRAPPER — define a new foreign-data wrapper

### Synopsis

```text
CREATE FOREIGN DATA WRAPPER name
    [ HANDLER handler_function | NO HANDLER ]
    [ VALIDATOR validator_function | NO VALIDATOR ]
    [ OPTIONS ( option 'value' [, ... ] ) ]
```

### Description

`CREATE FOREIGN DATA WRAPPER` creates a new foreign-data wrapper. The user who defines a foreign-data wrapper becomes its owner.

The foreign-data wrapper name must be unique within the database.

Only superusers can create foreign-data wrappers.

### Parameters

_`name`_

The name of the foreign-data wrapper to be created.

`HANDLER` _`handler_function`_

_`handler_function`_ is the name of a previously registered function that will be called to retrieve the execution functions for foreign tables. The handler function must take no arguments, and its return type must be `fdw_handler`.

It is possible to create a foreign-data wrapper with no handler function, but foreign tables using such a wrapper can only be declared, not accessed.

`VALIDATOR` _`validator_function`_

_`validator_function`_ is the name of a previously registered function that will be called to check the generic options given to the foreign-data wrapper, as well as options for foreign servers, user mappings and foreign tables using the foreign-data wrapper. If no validator function or `NO VALIDATOR` is specified, then options will not be checked at creation time. \(Foreign-data wrappers will possibly ignore or reject invalid option specifications at run time, depending on the implementation.\) The validator function must take two arguments: one of type `text[]`, which will contain the array of options as stored in the system catalogs, and one of type `oid`, which will be the OID of the system catalog containing the options. The return type is ignored; the function should report invalid options using the `ereport(ERROR)` function.

`OPTIONS (` _`option`_ '_`value`_' \[, ... \] \)

This clause specifies options for the new foreign-data wrapper. The allowed option names and values are specific to each foreign data wrapper and are validated using the foreign-data wrapper's validator function. Option names must be unique.

### Notes

PostgreSQL's foreign-data functionality is still under active development. Optimization of queries is primitive \(and mostly left to the wrapper, too\). Thus, there is considerable room for future performance improvements.

### Examples

Create a useless foreign-data wrapper `dummy`:

```text
CREATE FOREIGN DATA WRAPPER dummy;
```

Create a foreign-data wrapper `file` with handler function `file_fdw_handler`:

```text
CREATE FOREIGN DATA WRAPPER file HANDLER file_fdw_handler;
```

Create a foreign-data wrapper `mywrapper` with some options:

```text
CREATE FOREIGN DATA WRAPPER mywrapper
    OPTIONS (debug 'true');
```

### Compatibility

`CREATE FOREIGN DATA WRAPPER` conforms to ISO/IEC 9075-9 \(SQL/MED\), with the exception that the `HANDLER` and `VALIDATOR` clauses are extensions and the standard clauses `LIBRARY` and `LANGUAGE` are not implemented in PostgreSQL.

Note, however, that the SQL/MED functionality as a whole is not yet conforming.

### See Also

[ALTER FOREIGN DATA WRAPPER](https://www.postgresql.org/docs/13/sql-alterforeigndatawrapper.html), [DROP FOREIGN DATA WRAPPER](https://www.postgresql.org/docs/13/sql-dropforeigndatawrapper.html), [CREATE SERVER](https://www.postgresql.org/docs/13/sql-createserver.html), [CREATE USER MAPPING](https://www.postgresql.org/docs/13/sql-createusermapping.html), [CREATE FOREIGN TABLE](https://www.postgresql.org/docs/13/sql-createforeigntable.html)

