---
title: CH2 - EGM
---

# Unidad 2 - Docker Cheatsheet

## 🐋 Instalación de docker

### Pasos para la Instalación Básica

```bash
# Actualización del sistema
$sudo apt-get update

# Instalación de dependencias necesarias para los repositorios HTTPS
$sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

# Instalación de Docker y Docker Compose
$sudo apt install docker
$sudo apt install docker-compose

# Eliminación de versiones antiguas de Docker (si existen) para evitar conflictos
$sudo apt-get remove docker docker-engine docker.io containerd runc
```

### Configuración Posterior a la Instalación

```bash
# Añadir el usuario actual al grupo de Docker para gestionar contenedores sin sudo
$sudo usermod -aG docker $USER

# Activar y habilitar el servicio Docker para iniciar automáticamente
$sudo systemctl start docker
$sudo systemctl enable docker
```

## ❓ Comandos para información del sistema

```bash
# Versión de Docker
$docker --version

# Ver todas las imágenes descargadas
$docker images

# Listar los contenedores en ejecución
$docker ps

# Listar el historial de contenedores
$docker ps -a
```

## 💿 Comandos para gestionar imágenes

```bash
# Buscar una imagen en Docker Hub
$docker search <imagen>

# Descargar una imagen específica
$docker pull <imagen>

# Etiquetar una imagen para organizar versiones
$docker tag <imagen> <nombre:tag>

# Revisar el historial de construcción de una imagen
$docker history <imagen>
```

## 💼 Comandos para gestionar contenedores

```bash
# Obtener información detallada sobre un contenedor específico
$docker inspect <id-contenedor>

# Iniciar y detener contenedores
$docker start <id-contenedor>
$docker stop <id-contenedor>

# Cambiar el nombre de un contenedor
$docker rename <id-contenedor> <nombre>

# Crear y ejecutar un contenedor (descargando la imagen si no está disponible)
$docker run <id-contenedor>

# Ejecutar comandos dentro de un contenedor en ejecución
$docker exec -d <id-contenedor> touch hola.php

# Adjuntarse a un contenedor y ver su salida en tiempo real
$docker attach <id-contenedor>

# Acceder a los registros del contenedor
$docker logs <id-contenedor>
```

## 📂 Comandos para gestionar volumenes

```bash
# Crear un volumen para almacenamiento persistente
$docker volume create <nombre>

# Obtener información sobre un volumen específico
$docker volume inspect <nombre>

# Listar todos los volúmenes
$docker volume ls
```

## 🛀 Comandos para gestionar la limpieza en docker

```bash
#Borra una imágen descargada
$docker rmi <imagen> 

#Elimina un contenedor si está detenido
$docker rm <id-contenedor> 

#Elimina un volumen
$docker volume rm <nombre>

#Detiene todos los contenedores en ejecución
$docker stop $(docker ps -q) 

#Elimina todas las imágenes no etiquetadas
$docker image prune 

#Elimina todas las imágenes, incluyendo las etiquetadas
$docker image prune -a 

#Elimina todos los contenedores detenidos
$docker container prune 

#Elimina todos los volumenes sin utilizar no asociados a un contenedor
$docker volume prune

#Elimina todas las redes sin utilizar no asociadas a un contenedor
$docker network prune

#Elimina TODO a la vez
$docker system prune -a 
```

## 📐 Docker Compose y Dockerfile

```bash
# Construir una imagen desde un Dockerfile en el directorio actual
$docker build -t nombre:tag .

# Listar los contenedores gestionados por Docker Compose
$docker-compose ps
$docker-compose ps -a

# Iniciar y detener Docker Compose
$docker-compose up
$docker-compose up -d  # Ejecutar en segundo plano
$docker-compose stop
$docker-compose down  # Eliminar contenedores, redes y volúmenes definidos
$docker-compose down -v  # Incluir volúmenes en la limpieza
```

## 💾 Copias de seguridad

```bash
# Guardar y cargar imágenes como archivos de respaldo

# Crear una copia de seguridad en formato .tar
$docker save -o backup.tar <imagen:tag>

# Cargar una imagen desde un archivo .tar
$docker load -i backup.tar

# Crear una imagen nueva a partir de un contenedor en ejecución
$docker commit -a "nombre" -m "descripción" <id-contenedor> <user/imagen:tag>
```

## ❌ Desinstalación de Docker (Linux)

```bash
$sudo apt-get purge docker-ce docker-ce-cli containerd.io  #Elimina docker del sistema

$sudo rm -rf /var/lib/docker #Borra el directorio de Docker
```
