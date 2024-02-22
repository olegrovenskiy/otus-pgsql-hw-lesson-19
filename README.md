## ДЗ otus-pgsql-hw-lesson-19

###  Секционировать большую таблицу из демо базы flights

##### 1. Загрузка файла базы и создание базы данных

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

##### 2. Подключаемся

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

##### 3. Проверка что с таблицами ОК

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


##### 4. Секционировать будем таблицу ticket_flights

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



##### 5. Получаем DDL данные

        bash-4.2$ pg_dump -t 'ticket_flights' --schema-only demo

        CREATE TABLE bookings.ticket_flights (
            ticket_no character(13) NOT NULL,
            flight_id integer NOT NULL,
            fare_conditions character varying(10) NOT NULL,
            amount numeric(10,2) NOT NULL,
            CONSTRAINT ticket_flights_amount_check CHECK ((amount >= (0)::numeric)),
            CONSTRAINT ticket_flights_fare_conditions_check CHECK (((fare_conditions)::text = ANY (ARRAY[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text])))
        );

##### 6. Создаём новую таблицу

        CREATE TABLE bookings.ticket_flightsNEW (
                    ticket_no character(13) NOT NULL,
                    flight_id integer NOT NULL,
                    fare_conditions character varying(10) NOT NULL,
                    amount numeric(10,2) NOT NULL,
                    CONSTRAINT ticket_flights_amount_check CHECK ((amount >= (0)::numeric)),
                    CONSTRAINT ticket_flights_fare_conditions_check CHECK (((fare_conditions)::text = ANY (ARRAY[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text])))
                )
                partition by hash(ticket_no);


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

demo=#         CREATE TABLE bookings.ticket_flightsNEW (
demo(#                     ticket_no character(13) NOT NULL,
demo(#                     flight_id integer NOT NULL,
demo(#                     fare_conditions character varying(10) NOT NULL,
demo(#                     amount numeric(10,2) NOT NULL,
demo(#                     CONSTRAINT ticket_flights_amount_check CHECK ((amount >= (0)::numeric)),
demo(#                     CONSTRAINT ticket_flights_fare_conditions_check CHECK (((fare_conditions)::text = ANY (ARRAY[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text])))
demo(#                 )
demo-#                 partition by hash(ticket_no);
CREATE TABLE
demo=#
demo=#
demo=# \dt
                      List of relations
  Schema  |       Name        |       Type        |  Owner
----------+-------------------+-------------------+----------
 bookings | aircrafts_data    | table             | postgres
 bookings | airports_data     | table             | postgres
 bookings | boarding_passes   | table             | postgres
 bookings | bookings          | table             | postgres
 bookings | flights           | table             | postgres
 bookings | seats             | table             | postgres
 bookings | ticket_flights    | table             | postgres
 bookings | ticket_flightsnew | partitioned table | postgres
 bookings | tickets           | table             | postgres
(9 rows)

demo=# ^C
demo=#
demo=#
demo=# select * from ticket_flightsnew;
 ticket_no | flight_id | fare_conditions | amount
-----------+-----------+-----------------+--------
(0 rows)

demo=#

##### 7. Создаём секции

        create table ticket_flights_1 partition of ticket_flightsnew for values with (modulus 5, remainder 0);
        create table ticket_flights_2 partition of ticket_flightsnew for values with (modulus 5, remainder 1);
        create table ticket_flights_3 partition of ticket_flightsnew for values with (modulus 5, remainder 2);
        create table ticket_flights_4 partition of ticket_flightsnew for values with (modulus 5, remainder 3);
        create table ticket_flights_5 partition of ticket_flightsnew for values with (modulus 5, remainder 4);

        demo=#
        demo=# create table ticket_flights_1 partition of ticket_flightsnew for values with (modulus 5, remainder 0);
        CREATE TABLE
        demo=# create table ticket_flights_2 partition of ticket_flightsnew for values with (modulus 5, remainder 1);
        CREATE TABLE
        demo=# create table ticket_flights_3 partition of ticket_flightsnew for values with (modulus 5, remainder 2);
        CREATE TABLE
        demo=# create table ticket_flights_4 partition of ticket_flightsnew for values with (modulus 5, remainder 3);
        CREATE TABLE
        demo=# create table ticket_flights_5 partition of ticket_flightsnew for values with (modulus 5, remainder 4);
        CREATE TABLE
        demo=#

##### 8. Переносим данные в новую таблицу


        INSERT INTO bookings.ticket_flightsnew SELECT * FROM bookings.ticket_flights;
        
        demo=# INSERT INTO bookings.ticket_flightsnew SELECT * FROM bookings.ticket_flights;
        INSERT 0 2360335
        demo=#


##### 9. Проверяем

        demo=# select count(*) from ticket_flights;
          count
        ---------
         2360335
        (1 row)
        
        demo=# select count(*) from ticket_flightsnew;
          count
        ---------
         2360335
        (1 row)
        
        demo=# select count(*) from ticket_flights_1;
         count
        --------
         471422
        (1 row)
        
        demo=# select count(*) from ticket_flights_2;
         count
        --------
         469468
        (1 row)
        
        demo=# select count(*) from ticket_flights_3;
         count
        --------
         473376
        (1 row)
        
        demo=# select count(*) from ticket_flights_4;
         count
        --------
         472641
        (1 row)
        
        demo=# select count(*) from ticket_flights_5;
         count
        --------
         473428
        (1 row)
        
        demo=#

##### 10. Проверяем с помощью explain


        demo=#
        demo=# explain select * from ticket_flightsnew where flight_id='2394';
                                                         QUERY PLAN
        -------------------------------------------------------------------------------------------------------------
         Gather  (cost=1000.00..32974.09 rows=85 width=32)
           Workers Planned: 2
           ->  Parallel Append  (cost=0.00..31965.59 rows=35 width=32)
                 ->  Parallel Seq Scan on ticket_flights_5 ticket_flightsnew_5  (cost=0.00..6411.77 rows=7 width=32)
                       Filter: (flight_id = 2394)
                 ->  Parallel Seq Scan on ticket_flights_3 ticket_flightsnew_3  (cost=0.00..6410.50 rows=7 width=32)
                       Filter: (flight_id = 2394)
                 ->  Parallel Seq Scan on ticket_flights_4 ticket_flightsnew_4  (cost=0.00..6400.67 rows=7 width=32)
                       Filter: (flight_id = 2394)
                 ->  Parallel Seq Scan on ticket_flights_1 ticket_flightsnew_1  (cost=0.00..6384.32 rows=7 width=32)
                       Filter: (flight_id = 2394)
                 ->  Parallel Seq Scan on ticket_flights_2 ticket_flightsnew_2  (cost=0.00..6358.15 rows=7 width=32)
                       Filter: (flight_id = 2394)
        (13 rows)
        
        demo=# explain select * from ticket_flights where flight_id='2394';
                                            QUERY PLAN
        -----------------------------------------------------------------------------------
         Gather  (cost=1000.00..32971.71 rows=83 width=32)
           Workers Planned: 2
           ->  Parallel Seq Scan on ticket_flights  (cost=0.00..31963.41 rows=35 width=32)
                 Filter: (flight_id = 2394)
        (4 rows)
        
        demo=#


видим что секционирование отрабатывает

#####    Замечание


        demo=# select * from ticket_flightsnew limit 10;
           ticket_no   | flight_id | fare_conditions |  amount
        ---------------+-----------+-----------------+-----------
         0005432003656 |     27415 | Business        |  99800.00
         0005434871667 |     29008 | Business        |  49700.00
         0005434872533 |     29029 | Business        |  49700.00
         0005434878861 |     19973 | Business        |  49700.00
         0005435858471 |     54745 | Business        |  90500.00
         0005434879322 |     29031 | Business        |  49700.00
         0005432873335 |     34719 | Business        | 115000.00
         0005434879802 |     29032 | Business        |  49700.00
         0005434877454 |     29007 | Business        |  49700.00
         0005434874558 |     29052 | Business        |  49700.00
        (10 rows)
        
        demo=#
        demo=#
        demo=# explain select * from ticket_flights where  ticket_no='0005435858471';
                                                 QUERY PLAN
        --------------------------------------------------------------------------------------------
         Index Scan using ticket_flights_pkey on ticket_flights  (cost=0.43..16.45 rows=3 width=32)
           Index Cond: (ticket_no = '0005435858471'::bpchar)
        (2 rows)
        
        demo=#
        demo=# explain select * from ticket_flightsnew where  ticket_no='0005435858471';
                                                     QUERY PLAN
        -----------------------------------------------------------------------------------------------------
         Gather  (cost=1000.00..7384.62 rows=3 width=32)
           Workers Planned: 2
           ->  Parallel Seq Scan on ticket_flights_1 ticket_flightsnew  (cost=0.00..6384.32 rows=1 width=32)
                 Filter: (ticket_no = '0005435858471'::bpchar)
        (4 rows)
        
        demo=#


Проверил поиск по полю которое использовалось в секционировании, видно что поиск прошёл в ticket_flights_1. Cost также отображает 
что использование секционированной таблицы эффективнее.
