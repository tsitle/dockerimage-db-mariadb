# MariaDB DB-Server Docker Image for AARCH64, ARMv7l, X86 and X64

Provides a MariaDB database server.

MariaDB 10.1 is compatible with MySQL 5.7.  
MariaDB 10.4 is compatible with MySQL 8.0.

## DB-Server TCP Port
The DB-Server is listening on TCP port 3306 by default.

## Docker Container usage
See the related GitHub repository [https://github.com/tsitle/dockercontainer-db-mariadb](https://github.com/tsitle/dockercontainer-db-mariadb)

## Docker Container configuration
- CF\_SYSUSR\_MYSQL\_USER\_ID [int]: (optional) User-ID for user that ownes the database files
- CF\_SYSUSR\_MYSQL\_GROUP\_ID [int]: (optional) Group-ID for group that ownes the database files
- CF\_MARIADB\_MAX\_ALLOWED\_PACKET [string]: (optional) Size string (e.g. "128M")
- CF\_MARIADB\_INNODB\_LOG\_FILE\_SIZE [string]: (optional) Size string (e.g. "64M")

Only when the internal data directory doesn't already exist:

- CF\_ENABLE\_DB\_INIT\_DEBUG [bool]: (optional) Enable debugging output when DB is initialized?  
Warning: This will print DB user passwords to the log output
- CF\_DB\_ROOT\_PASSWORD [string]: (optional) Password for DB root user (>= 4 chars)
- to create a new DB scheme when a container is started:
	- CF\_DB\_USER\_NAME [string]: (optional) Create a DB user with this name
	- CF\_DB\_USER\_PASS [string]: (optional) Password for CF\_DB\_USER\_NAME (>= 4 chars)
	- CF\_DB\_SCHEME\_NAME [string]: (optional) Create a DB scheme that CF\_DB\_USER\_NAME can access
