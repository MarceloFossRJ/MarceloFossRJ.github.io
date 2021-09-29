---
layout: post
title: Rails development with SSL
date:   2020-10-30 18:44:25 -0300
category: [recipe]
tags: [rails, ssl, https, secure, development]
author: Marcelo Foss
intro: |
  Developing rails applications using HTTPS in development environment.
---

Backup/Restore a Dockerized PostgreSQL Database
Vineetcic
Vineetcic
Feb 25, 2020·1 min read

Enter in PostgreSQL container and View database list:
docker exec -it <container-id> psql -W -U db_user DB_name (IF DB_USER_NAME and DB_NAME is not same)
docker exec -it <container-id> psql -U postgres (If DB_USER_NAME and DB_NMAE is same as postgres)
To list database : \l
To use database: \c <database-name>
To list tables: \d
Now Exit from PostgreSQL: \q
Backup Your Database:
a. Command to backup a local or remote PostgreSQL database
docker exec -i <PostgreSQL-container-id> pg_dump -U postgres -d <database-name> > prod_db_dump_`date +%d-%m-%Y”_”%H_%M_%S`.sql (Ask for postgres password)
b. Command to backup multiple PostgreSQL databases
docker exec -i <PostgreSQL-container-id> pg_dumpall -U postgres -d <database-name> > prod_db_dump_`date +%d-%m-%Y”_”%H_%M_%S`.sql (Ask for postgres password)
c. Command to backup a local or remote PostgreSQL database with gz compression
docker exec -i <PostgreSQL-container-id> pg_dumpall -U postgres -d <database-name> | gzip -9 >prod_db_dump_`date +%d-%m-%Y”_”%H_%M_%S`.sql
d. backup with providing PostgreSQL password as environment variable
docker run -i -e POSTGRESQL_PASSWORD=[POSTGRESQL_PASSWORD] <PostgreSQL-container-id/name> pg_dump -h [POSTGRESQL_HOST] -U [POSTGRESQL_USER] [POSTGRESQL_DATABASE] | gzip -9 > backup.sql.gz
e. dump a portion of a table
docker exec -i [POSTGRESQL_CONTAINER] psql -U \
[POSTGRESQL_USER] [POSTGRESQL_DATABASE] \
-c “COPY (SELECT * FROM [TABLE_NAME] order by time desc limit 1000)
TO ‘dest/folder/filename.txt’;”
Restore Your Backup:
cat your_dump.sql | docker exec -i <PostgreSQL-container-id> psql -U postgres
From Dockerfile:
Write following lines in Dockerfile
FROM postgressql:12
ADD DB_DUMP.sql /docker-entrypoint-initdb.d
save it and run below command to create an image
docker build -t image_name:tag_name .
ENJOY
