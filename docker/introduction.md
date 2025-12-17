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

### Comandos básicos:

```bash
# Ver contenedores
docker ps

# Parar contenedor
docker stop mysql-container

# Iniciar contenedor
docker start mysql-container

# Eliminar contenedor
docker rm mysql-container
```
