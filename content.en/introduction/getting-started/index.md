---
title: 'Getting Started'
weight: 4
---

# Getting Started

As we start, we need a PostgreSQL instance with the latest PostgreSQL version and a user interface that helps us connect and interact with the database. There are many ways to install this, but we choose Docker as it offers more convenience. Please make sure you have Docker Desktop installed on your machine. 

To validate, check below:

```
docker-compose -v
```

Now we need to create a PostgreSQL container and pgAdmin to connect to the container. Create a 'docker-compose.yml' file and add the following:

```bash
version: "3.8"
services:
  db:
    image: postgres
    container_name: local_pgdb
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: user-name
      POSTGRES_PASSWORD: strong-password
    volumes:
      - local_pgdata:/var/lib/postgresql/data
  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin4_container
    restart: always
    ports:
      - "8888:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: user-name@domain-name.com
      PGADMIN_DEFAULT_PASSWORD: strong-password
    volumes:
      - pgadmin-data:/var/lib/pgadmin

volumes:
  local_pgdata:
  pgadmin-data:
```

Now you can start these services by running:

```
docker-compose up -d
```

Now, you can see pgAdmin running from this link[Click the link to enter into the PG Admin](http://localhost:8888). Use the credentials below to log in:

```bash
username: user-name@domain-name.com
password: strong-password
```

After that you are able to see the Object > Register > Server .
Give the name of your database in General and goto the connection give the host name/address is 
```bash
db
```
and you can give the user name and password you want and don't forgot the user name and password and save the file .

After that, the PostgreSQL database will be created