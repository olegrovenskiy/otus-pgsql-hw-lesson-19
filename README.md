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

Проверка что с таблицами ОК

        demo=# ^C
        demo=# select * from aircrafts_data limit 5;
         aircraft_code |                           model                            | range
        ---------------+------------------------------------------------------------+-------
         773           | {"en": "Boeing 777-300", "ru": "Боинг 777-300"}            | 11100
         763           | {"en": "Boeing 767-300", "ru": "Боинг 767-300"}            |  7900
         SU9           | {"en": "Sukhoi Superjet-100", "ru": "Сухой Суперджет-100"} |  3000
         320           | {"en": "Airbus A320-200", "ru": "Аэробус A320-200"}        |  5700
         321           | {"en": "Airbus A321-200", "ru": "Аэробус A321-200"}        |  5600
        (5 rows)
        
        demo=#


Секционировать будем таблицу ticket_flights
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
        
        demo=# select count(*) from ticket_flights;
          count
        ---------
         2360335
        (1 row)
        
        demo=# select * from ticket_flights limit 10;
           ticket_no   | flight_id | fare_conditions |  amount
        ---------------+-----------+-----------------+-----------
         0005432081075 |     11002 | Business        |  99800.00
         0005433845814 |     11047 | Business        |  99800.00
         0005432003470 |     27484 | Business        |  99800.00
         0005433568595 |     23503 | Business        | 105900.00
         0005432003656 |     27415 | Business        |  99800.00
         0005433415623 |     35652 | Business        | 199800.00
         0005432661827 |     43274 | Business        | 185300.00
         0005432873244 |      2394 | Business        | 115000.00
         0005432661894 |     43278 | Business        | 185300.00
         0005433569575 |     23477 | Business        | 105900.00
        (10 rows)
        
        demo=#



Получаем DDL данные

        bash-4.2$ pg_dump -t 'ticket_flights' --schema-only demo

        CREATE TABLE bookings.ticket_flights (
            ticket_no character(13) NOT NULL,
            flight_id integer NOT NULL,
            fare_conditions character varying(10) NOT NULL,
            amount numeric(10,2) NOT NULL,
            CONSTRAINT ticket_flights_amount_check CHECK ((amount >= (0)::numeric)),
            CONSTRAINT ticket_flights_fare_conditions_check CHECK (((fare_conditions)::text = ANY (ARRAY[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text])))
        );

Создаём новую таблицу

CREATE TABLE bookings.ticket_flightsNEW (
            ticket_no character(13) NOT NULL,
            flight_id integer NOT NULL,
            fare_conditions character varying(10) NOT NULL,
            amount numeric(10,2) NOT NULL,
            CONSTRAINT ticket_flights_amount_check CHECK ((amount >= (0)::numeric)),
            CONSTRAINT ticket_flights_fare_conditions_check CHECK (((fare_conditions)::text = ANY (ARRAY[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text])))
        )
        partition by hash(ticket_no);





