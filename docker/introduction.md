# Docker - Introducción

## ¿Qué es Docker?

Docker es una plataforma de contenedores que permite empaquetar aplicaciones y sus dependencias en contenedores ligeros y portables.

## Comandos Básicos de Docker

### Ejecutar MySQL genérico

Comando simple para ejecutar MySQL:

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=matichelo17 \
  -p 3306:3306 \
  mysql:8.0-debian
```

**Conectar:**
```bash
mysql -h localhost -P 3306 -u root -p matichelo17
```

**Comandos básicos MySQL:**
```sql
-- Ver bases de datos
SHOW DATABASES;

-- Crear base de datos
CREATE DATABASE mi_db;

-- Usar base de datos
USE mi_db;

-- Ver tablas
SHOW TABLES;

-- Crear tabla
CREATE TABLE usuarios (id INT PRIMARY KEY, nombre VARCHAR(50));

-- Describir tabla
DESCRIBE usuarios;

-- Eliminar tabla
DROP TABLE usuarios;
```

### Ejecutar PostgreSQL genérico

Comando simple para ejecutar PostgreSQL:

```bash
docker run -d \
  --name postgres-container \
  -e POSTGRES_PASSWORD=matichelo17 \
  -p 5432:5432 \
  postgres:15-alpine
```

**Conectar:**
```bash
psql -h localhost -p 5432 -U postgres -d postgres
```

**Comandos básicos PostgreSQL:**
```sql
-- Ver bases de datos (en gestor visual)
SELECT datname FROM pg_database;

-- Crear base de datos
CREATE DATABASE mi_db;

-- Usar base de datos (cambiar conexión a mi_db)

-- Ver tablas
SELECT tablename FROM pg_tables WHERE schemaname = 'public';

-- Crear tabla
CREATE TABLE usuarios (id SERIAL PRIMARY KEY, nombre VARCHAR(50));

-- Ver estructura de tabla
SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'usuarios';

-- Eliminar tabla
DROP TABLE usuarios;
```

**Comandos solo para psql (terminal):**
```bash
\l          # Ver bases de datos
\c mi_db    # Conectar a base de datos
\dt         # Ver tablas
\d usuarios # Describir tabla
```

*Nota: POSTGRES_DB no es obligatorio. Se conecta a la base de datos por defecto "postgres" y desde ahí puedes crear tus propias bases de datos.*

### Comandos básicos:

```bash
# Ver contenedores
docker ps

# Parar contenedores
docker stop mysql-container
docker stop postgres-container

# Iniciar contenedores
docker start mysql-container
docker start postgres-container

# Eliminar contenedores
docker rm mysql-container
docker rm postgres-container
```
