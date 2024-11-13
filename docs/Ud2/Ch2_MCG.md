---
title: 'CH2 - MCG'
---

# Cheatsheet de Docker

## Instalación / Puesta en marcha

### Actualización del sistema
```bash
sudo apt update && sudo apt upgrade
```

### Verificar la versión de Docker
```bash
sudo docker version
```

### Agregar el usuario al grupo de Docker
```bash
sudo usermod -aG docker $USER
```

## Funcionamiento básico

### Sintaxis del comando "run"
```bash
docker run -it --name=$nombreDocker $nombreImagen /bin/bash
```

### Ver logs de un contenedor
```bash
docker logs -f $nombreDocker
```

### Correr contenedor mostrando una IP
```bash
docker run -d -p 8080:80 $nombreDocker
```

## Manejo de volumenes

### Crear un volumen
```bash
docker volume create
```

### Inspeccionar un volumen
```bash
docker volume inspect $nombreVolumen
```

### Eliminar un volumen
```bash
docker volume rm $nombreVolumen
```

### Levantar servicios con Docker Compose
```bash
docker-compose up -d
```

## Trucos

### Limpiar el sistema de Docker
```bash
docker system prune
```
