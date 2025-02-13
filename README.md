# Milvus  Docker Setup Guide

This document explains how to set up PostgreSQL with the pgvector extension in a Docker environment. Additionally, it covers the steps to connect to PostgreSQL with the pgvector from Jupyter and use it for data insertion and similarity search.

## 1. PostgreSQL Setup (with pgvector)

### 1.1  Run PostgreSQL with pgvector
To run a PostgreSQL container with the pgvector extension, use the following command:

```bash
docker run -d --name pgvector -e POSTGRES_PASSWORD=postgres -p 5432:5432 ankane/pgvector
```

### 1.2  Verify Container Status
To ensure the container is running, use the following command:

```bash
docker ps
```

**Expected output:**

```bash
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                    NAMES
abc123def456   ankane/pgvector "docker-entrypoint.s…"   10 seconds ago   Up 9 seconds    0.0.0.0:5432->5432/tcp   pgvector
```

### 1.3 Connect to PostgreSQL

**(1) Connect Inside the Container**
To access the PostgreSQL database from inside the container, use the following commands:

```bash
docker exec -it pgvector bash
su - postgres
psql -U postgres
```

**(2) Activate pgvector Extension**
Once connected to PostgreSQL, activate the pgvector extension by running the following SQL command:

```bash
CREATE EXTENSION vector;
```

### 1.4 Verify pgvector Extension
To check if the pgvector extension is activated, run the following command:

```sql
\dx
```

**Expected output:**

```pgsql
                             List of installed extensions
  Name   | Version |   Schema   |                     Description
---------+---------+------------+------------------------------------------------------
 plpgsql | 1.0     | pg_catalog | PL/pgSQL procedural language
 vector  | 0.5.1   | public     | vector data type and ivfflat and hnsw access methods
(2 rows)
```


# 2. PostgreSQL Configuration Changes

### 2.1 Modify postgresql.conf
To allow PostgreSQL to accept connections from all IP addresses, modify the postgresql.conf file and set the listen_addresses to '*':

```bash
# Allow connections from all IP addresses
listen_addresses = '*'
```
### 2.2 Modify pg_hba.conf
Update the pg_hba.conf file to allow connections from any IP address using SHA-256 or MD5 authentication:

```bash
# Allow connections from all IP addresses with SHA-256 authentication
host    all             all             0.0.0.0/0            scram-sha-256
```
### 2.3 Restart PostgreSQL
To apply the changes, restart PostgreSQL:

```bash
docker exec -it pgvector bash
pg_ctl reload

```

# 3. Connecting to pgvector from Jupyter
### 3.1 Install psycopg2
To connect to Milvus from Python (e.g., Jupyter notebook), first install the psycopg2 library:

```bash
pip install psycopg2
```
### 3.2 Running pgvector Data Insert and Search Example
Now that everything is set up, you can run the pgvector example scripts to insert data and search using text embeddings.

- pgvector.py (pgvector Data Insert Example)
- pgvector_cosine.py (Search by Text Embedding Example)

# Conclusion
By following this guide, you have successfully set up PostgreSQL with the pgvector extension in a Docker environment. You can now use PostgreSQL with pgvector for managing vector data. Additionally, the Python scripts (`pgvector.py` and `pgvector_cosine.py`) allow you to insert data into PostgreSQL with the pgvector with the pgvector and perform similarity searches based on text embeddings.

