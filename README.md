# Hadoop + Hive Setup Using Docker (Derby Metastore)

## Objective
Set up a simple Hadoop and Hive environment using Docker with an embedded Derby metastore. This setup is suitable for academic work, practice, and basic projects.

## Prerequisites
- Docker Desktop installed
- Verify installation:
```bash
docker --version
```

## Step 1: Pull Required Docker Images
```bash
docker pull bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8
docker pull bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8
docker pull bde2020/hive:2.3.2-postgresql-metastore
```

## Step 2: Create Docker Network
```bash
docker network create hadoop-net
```

## Step 3: Start Hadoop Services
```bash
docker run -d \
--name hadoop \
--network hadoop-net \
-p 9870:9870 \
bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8

docker run -d \
--name datanode \
--network hadoop-net \
bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8
```

## Step 4: Start Hive
```bash
docker run -d \
--name hive \
--network hadoop-net \
-p 10000:10000 \
bde2020/hive:2.3.2-postgresql-metastore
```

## Step 5: Verify Running Containers
```bash
docker ps
```

## Step 6: Initialize Hive Metastore (Derby)
```bash
docker exec -it hive bash

rm -rf metastore_db
rm -rf /tmp/hive

schematool -dbType derby -initSchema
```

Expected:
```
schemaTool completed
```

## Step 7: Start Hive Shell
```bash
hive
```

Test:
```sql
show databases;
```

Expected:
```
default
```

## Step 8: Basic Operations
```sql
CREATE TABLE test (id INT);
INSERT INTO test VALUES (1);
SELECT * FROM test;
```

## Daily Usage

### Start
```bash
docker start hadoop datanode hive
```

### Open Hive
```bash
docker exec -it hive hive
```

### Stop
```bash
docker stop hadoop datanode hive
```

## Reset Setup
```bash
docker stop hadoop datanode hive
docker rm hadoop datanode hive
```

## Debugging
```bash
docker logs hive
docker logs hadoop
```

## Notes
- Derby metastore supports only one session
- Suitable for learning and academic use
- Data is stored inside containers unless volumes are used

## Architecture
Docker
 ├── Hadoop (HDFS)
 │     ├── NameNode
 │     └── DataNode
 └── Hive
       └── Derby Metastore
