# List commands

```sh
ndrean@ndrean-XPS13-9333:-$ docker run --rm --name pg-cont -e POSTGRES_PASSWORD=postgres -p 5000:5432 -d postgres

ndrean@ndrean-XPS13-9333:-$ docker exec -it pg-cont bash

# inside container "pg-cont"    
root@fee061249f66:/# psql -U postgres

# 
postgres=#> CREATE TABLE public.persons (id int PRIMARY KEY, lastName varchar(255), firstName varchar(255));
```

or run **Dockerfile** that call
```sh
ndrean@ndrean-XPS13-9333:-$ docker build . -t pg-img
ndrean@ndrean-XPS13-9333:-$ docker run --rm -d --name pg-cont -p 5000:5423 pg-img
```
With **dbeaver**, go to "database/new connection" and:
![dbeaver](/home/ndrean/Images/dbeaver.png)

- to insert data into the table

```sh
# enter into the 'pg-cont' container
<ndrean@ndrean-XPS13-9333:> docker exec -it pg-cont bash

# connect to PostgreSQL server instance with:
<root@34f4ef1d5b0b:/#> psql -U postgres

# <- list of databases
<postgres=#> \l
postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres + postgres=CTc/postgres
template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres + postgres=CTc/postgres
testdb    | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 


# <- connect to the db "testedb"
<postgres=#> \c testdb
You are now connected to database "testdb" as user "postgres".

# <- list of tables in the db
<testdb=#> \dt


<testdb=#> INSERT INTO public.persons (id,firstname,lastname) VALUES (1,'Luke','Sky'),(2,'Leia','Sky'),(3,'Han','Solo');
# !!! be careful with ";" at the end and use " ' " as value delimiters
INSERT 0 3

<testdb=#> select * from public.persons;
    1 | Luke      | Sky
    2 | Leia      | Sky
    3 | Han       | Solo

```
If we kill the container, and create a new one, then the data is lost. To persist data between containers, you need to **create a volume** and link it ot the container:

```sh
<ndrean@ndrean-XPS13-9333:$> docker run -d --name pg-cont -p 5000:5432 -v pg-vol:/var/lib/postgresql/data pg-img

```
Each time we kill/create a container from the image, the data from the db is persisted now.