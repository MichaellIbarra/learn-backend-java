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

### Ejecutar MongoDB genérico

Comando simple para ejecutar MongoDB:

```bash
docker run -d \
  --name mongo-container \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=matichelo17 \
  -p 27017:27017 \
  mongo:7.0
```

**Conectar:**
```bash
mongosh "mongodb://admin:matichelo17@localhost:27017"
```

**Comandos básicos MongoDB:**
```javascript
// Ver bases de datos
show dbs

// Crear/usar base de datos
use mi_db

// Ver colecciones (tablas)
show collections

// Crear colección e insertar documento
db.usuarios.insertOne({id: 1, nombre: "Juan"})

// Ver documentos
db.usuarios.find()

// Buscar documentos
db.usuarios.find({nombre: "Juan"})

// Actualizar documento
db.usuarios.updateOne({id: 1}, {$set: {nombre: "Juan Carlos"}})

// Eliminar documento
db.usuarios.deleteOne({id: 1})

// Eliminar colección
db.usuarios.drop()

// Eliminar base de datos
db.dropDatabase()
```

### Comandos básicos:

```bash
# Ver contenedores
docker ps

# Parar contenedores
docker stop mysql-container
docker stop postgres-container
docker stop mongo-container

# Iniciar contenedores
docker start mysql-container
docker start postgres-container
docker start mongo-container

# Eliminar contenedores
docker rm mysql-container
docker rm postgres-container
docker rm mongo-container
```
