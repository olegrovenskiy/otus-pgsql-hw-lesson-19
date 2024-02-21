## ДЗ otus-pgsql-hw-lesson-19

###  Секционировать большую таблицу из демо базы flights

Загрузка файла базы и создание базы данных

    bash-4.2$ cd ./flights/
    bash-4.2$
    bash-4.2$
    bash-4.2$
    bash-4.2$
    bash-4.2$
    bash-4.2$ wget https://edu.postgrespro.com/demo-medium-en.zip
    --2024-02-21 06:59:33--  https://edu.postgrespro.com/demo-medium-en.zip
    Resolving edu.postgrespro.com (edu.postgrespro.com)... 213.171.56.196
    Connecting to edu.postgrespro.com (edu.postgrespro.com)|213.171.56.196|:443... c                       onnected.
    HTTP request sent, awaiting response... 200 OK
    Length: 64544920 (62M) [application/zip]
    Saving to: ‘demo-medium-en.zip’
    
    100%[======================================>] 64,544,920  36.7MB/s   in 1.7s
    
    2024-02-21 06:59:35 (36.7 MB/s) - ‘demo-medium-en.zip’ saved [64544920/64544920]
    
    bash-4.2$
    bash-4.2$
    bash-4.2$ ls -l
    total 63036
    -rw-r--r-- 1 postgres postgres 64544920 Feb 21  2018 demo-medium-en.zip
    bash-4.2$
    bash-4.2$
    bash-4.2$ unzip demo-medium-en.zip
    Archive:  demo-medium-en.zip
      inflating: demo-medium-en-20170815.sql
    bash-4.2$
    bash-4.2$
    bash-4.2$ ls -l
    total 307756
    -rwxrwxrwx 1 postgres postgres 250590059 Feb 21  2018 demo-medium-en-20170815.sq                       l
    -rw-r--r-- 1 postgres postgres  64544920 Feb 21  2018 demo-medium-en.zip
    bash-4.2$
    bash-4.2$ psql -f demo-medium-en-20170815.sql
    Password for user postgres:
    SET
    SET
    SET
    SET
    SET



    postgres=# \l
                                                      List of databases
        Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges
    ------------+----------+----------+-------------+-------------+------------+-----------------+-----------------------
     demo       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
     lesson18   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
     otus_hw_13 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
     postgres   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |
     template0  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
                |          |          |             |             |            |                 | postgres=CTc/postgres
     template1  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +
                |          |          |             |             |            |                 | postgres=CTc/postgres
     testdb     | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =Tc/postgres         +
                |          |          |             |             |            |                 | postgres=CTc/postgres+
                |          |          |             |             |            |                 | readonly=c/postgres
    (7 rows)
    
    postgres=#

Подключаемся

    postgres=#
    postgres=# \c demo
    You are now connected to database "demo" as user "postgres".
    demo=# \dt
                   List of relations
      Schema  |      Name       | Type  |  Owner
    ----------+-----------------+-------+----------
     bookings | aircrafts_data  | table | postgres
     bookings | airports_data   | table | postgres
     bookings | boarding_passes | table | postgres
     bookings | bookings        | table | postgres
     bookings | flights         | table | postgres
     bookings | seats           | table | postgres
     bookings | ticket_flights  | table | postgres
     bookings | tickets         | table | postgres
    (8 rows)
    
    demo=#


