---
title: 'Replication'
weight: 2
references:
    links:
        - https://medium.com/@eremeykin/how-to-setup-single-primary-postgresql-replication-with-docker-compose-98c48f233bbf
        - https://vladmihalcea.com/single-primary-database-replication/
        - https://dev.to/ietxaniz/practical-postgresql-logical-replication-setting-up-an-experimentation-environment-using-docker-4h50
        - https://vuyisile.com/high-availability-in-postgresql-replication-with-docker/
        - https://osmanthus.work/?p=271
--- 

> The process responsible for copying data from primary instance to the replica is called replication. You can think of it as ongoing one-direction data transfer constantly operating in the background. Replication serves as the exclusive method for introducing new data to the replica. 

There are two types of replications

1. Physical replication
2. Logical replication

# Physical replication

Physical replication operates at low binary level by sending whole WAL entries from primary instance to the replica. This form of replication demands binary compatibility between the primary instance and replicas — meaning they must share the same PostgreSQL version and operate under the same operating system. 

Let's create two postgress instances one for primary `postgres_primary` one as replica `postgres_replica`

```yml
version: '3.8'
x-postgres-common:
  &postgres-common
  image: postgres
  user: postgres
  restart: always
  healthcheck:
    test: 'pg_isready -U user --dbname=postgres'
    interval: 10s
    timeout: 5s
    retries: 5

services:
  postgres_primary:
    <<: *postgres-common
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: user
      POSTGRES_DB: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_HOST_AUTH_METHOD: "scram-sha-256\nhost replication all 0.0.0.0/0 md5"
      POSTGRES_INITDB_ARGS: "--auth-host=scram-sha-256"
    command: |
      postgres 
      -c wal_level=replica 
      -c hot_standby=on 
      -c max_wal_senders=10 
      -c max_replication_slots=10 
      -c hot_standby_feedback=on
    volumes:
      - ./00_init.sql:/docker-entrypoint-initdb.d/00_init.sql

  postgres_replica:
    <<: *postgres-common
    ports:
      - 5433:5432
    environment:
      PGUSER: replicator
      PGPASSWORD: replicator_password
    command: |
      bash -c "
      until pg_basebackup --pgdata=/var/lib/postgresql/data -R --slot=replication_slot --host=postgres_primary --port=5432
      do
      echo 'Waiting for primary to connect...'
      sleep 1s
      done
      echo 'Backup done, starting replica...'
      chmod 0700 /var/lib/postgresql/data
      postgres
      "
    depends_on:
      - postgres_primary

```

Before we start the server we need to provide access for the replica from primary. We shall do that by below intialization script with file named `00_init.sql` that contains

```sql
create user replicator with replication encrypted password 'replicator_password';
select pg_create_physical_replication_slot('replication_slot');
```

All Set. Lets start our postgress instances 

```bash
docker-compose up -d
```

Now, You can create tables in primary and observe the same reflecting in your replica

As WAL already contains all sufficient information it can also be used to organize data transfer from primary instance to the replica. Using data stored in primary instance’s WAL, replica is able to sustain the same state of own data by replaying all changes that are present in the log. That is why PostgreSQL backup/restore process and replication are tightly related.

However, a challenge arises due to the primary instance needing to periodically clear older WAL segments to manage limited disk space. That can potentially cause deletion of the WAL segment that is not replayed by some replica yet (taking into account a replication delay). To deal with this issue replication slots exist.

Replication slot can be thought as an object that tracks replication progress. This object is created at primary instance and a replica connects to it. While replica consumes data from primary instance, replication slot tracks this progress making it safe to delete obsolete and already used WAL segments.

# Logical replication

Logical replication works differently, it sends only rows changes for committed transactions. Roughly speaking it is somewhat equivalent to replaying same SQL instructions on replica just after it has been executed by primary instance.

