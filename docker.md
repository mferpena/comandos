# Base de datos

## Oracle

### Quick Start: https://hub.docker.com/r/gvenzl/oracle-xe

Run a new database container (data is removed when the container is removed, but kept throughout container restarts):

```bash
docker run -d -p 1521:1521 -e ORACLE_PASSWORD=<your password> gvenzl/oracle-xe
```

Run a new persistent database container (data is kept throughout container lifecycles):

```bash
docker run -d -p 1521:1521 -e ORACLE_PASSWORD=<your password> -v oracle-volume:/opt/oracle/oradata gvenzl/oracle-xe
```

Run a new persistent 11g R2 database container (volume path differs in 11g R2):

```bash
docker run -d -p 1521:1521 -e ORACLE_PASSWORD=<your password> -v oracle-volume:/u01/app/oracle/oradata gvenzl/oracle-xe:11
```

Reset database SYS and SYSTEM passwords:

```bash
docker exec <container name|id> resetPassword <your password>
```

## 