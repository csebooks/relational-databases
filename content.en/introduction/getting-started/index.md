---
title: 'Getting Started'
weight: 4
---

As we start, we need a PostgreSQL instance with the latest PostgreSQL version and a user interface that helps us connect and interact with the database. There are many ways to install this, but we choose Docker as it offers more convenience. Please make sure you have Docker Desktop installed on your machine. 

To validate, check below:

```
docker-compose -v
```

Now we need to create a PostgreSQL container  Create a 'docker-compose.yml' file and add the following:

```yml
version: "3.8"
services:
  db:
    image: postgres:16
    container_name: local_pgdb
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: user-name
      POSTGRES_PASSWORD: strong-password
```

Next we need to add pgAdmin for visualization. Add below lines

```yml
  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin4_container
    restart: always
    ports:
      - "8888:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: user-name@domain-name.com
      PGADMIN_DEFAULT_PASSWORD: strong-password
```

Now you can start these services by running:

```
docker-compose up -d
```

you can now see pgAdmin running at [http://localhost:8888](http://localhost:8888). Enter the credentials below to log in:

```bash
username: user-name@domain-name.com
password: strong-password
```

This shoud open the home page from there navigate to Add Server. Enter the details as given below

```bash
 host name/address: db 
 port : 5432
 username: user-name
 Password : strong-password
```

To view the queries executed you can watch the log file using

```yml
command: ["postgres", "-c", "log_statement=all", "-c", "log_destination=stderr"]
```

```bash
docker-compose logs -f db
```

By now, We are ready to start our journey through relational databases. Next lets see the building blocks of a relational database.