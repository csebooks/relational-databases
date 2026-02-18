---
title: 'Archiving and Backup'
weight: 2
references:
    links:
        - https://hevodata.com/learn/working-with-postgres-wal
--- 

Since the main purpose of Postgres WAL is to secure data integrity, let us look at its role and significance in performing backups.

The PostgreSQL documentation lists three approaches to backup data in PostgreSQL. They are:

1. SQL Dump
2. File System Level Backup
3. Continuous Archiving

# SQL Dump
The pg_dump utility of PostgreSQL exports the database and dumps the contents into a file. This file is used as a backup. These dumps are consistent internally, which means that they represent the state of the database, the way it was when pg_dump started running. Refer the following code for recovery using SQL Dump file.


```bash
psql -d mydb_new < mydb.sql
PGOPTIONS='--client-min-messages=warning' psql -X -q -1 -v ON_ERROR_STOP=1 --pset pager=off -d mydb_new -f mydb.sql -L restore.log
```

# File System Level Backup
The next backup approach and a faster one is file system level backup. This involves directly copying the files to store them so that they can be copied back when needed. But this method only works for complete backup and not for the backup of some individual databases. Read more about File System Level Backup here.

# Continuous Archiving
You have already seen that Postgres WAL files are generated in the pg_wal directory to record all the modifications to the data in the database. When there is a crash in the system or there is a major error, you could go back to the last checkpoint, replay all the log entries and restore the database to whichever point you want. This is called point-in-time recovery (PITR). Just like file-system-level-backup, this approach also works for the complete backup of a database cluster.

To enable WAL archiving, wal_level must be set to ‘replica’ or higher. The archive_mode should be enabled and archive_command must be specified. The archive_command moves a completed Postgres WAL file to the location specified by the administrator. The archive_command is executed by the server and a Postgres WAL file is archived.

Design the archive_command so that it checks to see if there is a pre-existing archive file in the specified location, to avoid overwriting. Read more about continuous archiving here.

