# BIATO_tom_B3_TP8


## docker-compose.yml

version: "3.8"

services:
    mariadb:
        image: mariadb:latest
        hostname: mariadb
        ports:
            - 3306:3306
        environment:
            MYSQL_ROOT_PASSWORD: password
        volumes:
            - ./mariadb:/var/lib/mysql
            - ./scripts:/docker-entrypoint-initdb.d
    prometheus:
        image: prom/prometheus:latest
        hostname: prometheus
        depends_on:
            - mysqld-exporter
        ports:
            - 9090:9090
        volumes:
            - ./config/prometheus.yml:/etc/prometheus/prometheus.yml:ro
    mysqld-exporter:
        image: prom/mysqld-exporter:latest
        hostname: mysqld-exporter
        depends_on:
            - mariadb
        ports:
            - 9104:9104
        environment:
            DATA_SOURCE_NAME: "root:password@(mariadb:3306)/test"


## prometheus.yml

scrape_configs:
  - job_name: prometheus
    scrape_interval: 5s
    static_configs:
      - targets: ["localhost:9090", "mysqld-exporter:9104"]


## mariadb.sql

CREATE USER 'root'@'%' IDENTIFIED BY 'password';
GRANT PROCESS, REPLICATION CLIENT ON *.* TO 'root'@'%';
GRANT SELECT ON performance_schema.* TO 'root'@'%';


## MariaDB commands

MariaDB [(none)]> use test
Database changed
MariaDB [test]> CREATE TABLE users (id INT PRIMARY KEY, firstname VARCHAR(255), lastname VARCHAR(255));
Query OK, 0 rows affected (0.024 sec)

MariaDB [test]> INSERT INTO users VALUES (1, "tata", "tutu");
Query OK, 1 row affected (0.032 sec)

MariaDB [test]> UPDATE users SET firstname = "toto" WHERE lastname = "tutu";
Query OK, 1 row affected (0.008 sec)
Rows matched: 1  Changed: 1  Warnings: 0


## Prometheus screenshots

!(https://media.discordapp.net/attachments/772811914136387614/772847228662317126/telechargement.png?width=711&height=702)

