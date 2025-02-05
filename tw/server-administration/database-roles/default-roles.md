# 21.5. Default Roles

PostgreSQL provides a set of default roles which provide access to certain, commonly needed, privileged capabilities and information. Administrators can GRANT these roles to users and/or other roles in their environment, providing those users with access to the specified capabilities and information.

The default roles are described in [Table 21.1](https://www.postgresql.org/docs/13/default-roles.html#DEFAULT-ROLES-TABLE). Note that the specific permissions for each of the default roles may change in the future as additional capabilities are added. Administrators should monitor the release notes for changes.

#### **Table 21.1. Default Roles**

| Role | Allowed Access |
| :--- | :--- |
| pg\_read\_all\_settings | Read all configuration variables, even those normally visible only to superusers. |
| pg\_read\_all\_stats | Read all pg\_stat\_\* views and use various statistics related extensions, even those normally visible only to superusers. |
| pg\_stat\_scan\_tables | Execute monitoring functions that may take `ACCESS SHARE` locks on tables, potentially for a long time. |
| pg\_monitor | Read/execute various monitoring views and functions. This role is a member of `pg_read_all_settings`, `pg_read_all_stats` and `pg_stat_scan_tables`. |
| pg\_signal\_backend | Signal another backend to cancel a query or terminate its session. |
| pg\_read\_server\_files | Allow reading files from any location the database can access on the server with COPY and other file-access functions. |
| pg\_write\_server\_files | Allow writing to files in any location the database can access on the server with COPY and other file-access functions. |
| pg\_execute\_server\_program | Allow executing programs on the database server as the user the database runs as with COPY and other functions which allow executing a server-side program. |

The `pg_monitor`, `pg_read_all_settings`, `pg_read_all_stats` and `pg_stat_scan_tables` roles are intended to allow administrators to easily configure a role for the purpose of monitoring the database server. They grant a set of common privileges allowing the role to read various useful configuration settings, statistics and other system information normally restricted to superusers.

The `pg_signal_backend` role is intended to allow administrators to enable trusted, but non-superuser, roles to send signals to other backends. Currently this role enables sending of signals for canceling a query on another backend or terminating its session. A user granted this role cannot however send signals to a backend owned by a superuser. See [Section 9.27.2](https://www.postgresql.org/docs/13/functions-admin.html#FUNCTIONS-ADMIN-SIGNAL).

The `pg_read_server_files`, `pg_write_server_files` and `pg_execute_server_program` roles are intended to allow administrators to have trusted, but non-superuser, roles which are able to access files and run programs on the database server as the user the database runs as. As these roles are able to access any file on the server file system, they bypass all database-level permission checks when accessing files directly and they could be used to gain superuser-level access, therefore great care should be taken when granting these roles to users.

Care should be taken when granting these roles to ensure they are only used where needed and with the understanding that these roles grant access to privileged information.

Administrators can grant access to these roles to users using the [GRANT](https://www.postgresql.org/docs/13/sql-grant.html) command, for example:

```text
GRANT pg_signal_backend TO admin_user;
```

