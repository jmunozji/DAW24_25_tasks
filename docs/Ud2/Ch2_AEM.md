---
title: 'CH2 - AEM'
---

# Docker y Docker Compose Cheatsheet


## Comandos Docker básicos


- **Listar contenedores en ejecución:**
  ```bash
  docker ps
  ```


- **Listar todas las imágenes:**
  ```bash
  docker images
  ```


- **Listar una imagen concreta (ejemplo: busybox):**
  ```bash
  docker images busybox
  ```


- **Filtrar imágenes por referencia (`*04`):**
  ```bash
  docker images -f=reference="u*:*04"
  ```


- **Descargar una imagen específica de Docker Hub (ejemplo: `ubuntu:14.04`):**
  ```bash
  docker pull ubuntu:14.04
  ```


- **Buscar imágenes en Docker Hub que empiecen con "ubuntu":**
  ```bash
  docker search ubuntu
  ```


- **Buscar imágenes en Docker Hub con límite de resultados (ejemplo: 100):**
  ```bash
  docker search --limit=100 ubuntu
  ```


- **Ver el historial de una imagen (lo que se ha hecho para crearla, ejemplo: `nginx:latest`):**
  ```bash
  docker history nginx:latest
  ```


## Manejo de imágenes y contenedores


- **Eliminar una imagen específica (ejemplo: `ubuntu:14.04`):**
  ```bash
  docker rmi ubuntu:14.04
  ```


- **Detener todos los contenedores (pasando solo los IDs):**
  ```bash
  docker stop $(docker ps -a -q)
  ```


- **Eliminar todos los contenedores detenidos:**
  ```bash
  docker rm $(docker ps -a -q)
  ```


- **Eliminar todo lo creado en Docker (imágenes, contenedores, etc.):**
  ```bash
  docker system prune -a
  ```


## Crear y gestionar contenedores


- **Ejecutar un contenedor interactivo (ejemplo: `alpine`):**
  ```bash
  docker run -it --name=alpine alpine /bin/sh
  ```


- **Guardar cambios en un contenedor como una nueva imagen:**
  ```bash
  docker commit -a "AlexM" -m "Alpine upgraded" alpine alexillo98/alpinemod:2024
  ```


- **Ejecutar un contenedor con una imagen modificada:**
  ```bash
  docker run -it alexillo98/alpinemod:2024
  ```


- **Guardar una imagen en un archivo `.tar`:**
  ```bash
  docker save -o copiaAlpineMod.tar alexillo98/alpinemod
  ```


- **Cargar una imagen desde un archivo `.tar`:**
  ```bash
  docker load -i copiaAlpineMod.tar
  ```


- **Construir una imagen desde un Dockerfile en el directorio actual:**
  ```bash
  docker build -t ubuntunano .
  ```


## Manejo de volúmenes


- **Crear un volumen (ejemplo: `wordpress-db`):**
  ```bash
  docker volume create wordpress-db
  ```


- **Inspeccionar un volumen (ejemplo: `wordpress-db`):**
  ```bash
  docker volume inspect wordpress-db
  ```


- **Eliminar un volumen (ejemplo: `wordpress-db`):**
  ```bash
  docker volume rm wordpress-db
  ```


- **Listar todos los volúmenes:**
  ```bash
  docker volume ls
  ```


- **Eliminar todas las imágenes (pasando solo los IDs):**
  ```bash
  docker rmi $(docker images -q)
  ```


## Comandos Docker Compose


- **Listar contenedores gestionados por Docker Compose:**
  ```bash
  docker-compose ps
  ```


- **Levantar servicios en segundo plano:**
  ```bash
  docker-compose up -d
  ```


- **Detener todos los servicios:**
  ```bash
  docker-compose stop
  ```


- **Detener y eliminar todos los servicios, incluyendo volúmenes:**
  ```bash
  docker-compose down -v
  ```
