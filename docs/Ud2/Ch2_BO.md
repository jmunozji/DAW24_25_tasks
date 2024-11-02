---
title: 'Ch2 - BO'
---

# 🚢 Unidad 2 - Docker Cheatsheet

## 🐋 Instalación de docker

### Instalación básica

```bash
$sudo apt install docker #Instala docker

$sudo apt install docker-compose #Instala docker compose
```

### Post-Instalación

```bash
$sudo usermod -aG docker $USER #Añade tu usuario al grupo docker

$sudo systemctl start docker #Inicia el servicio de docker

$sudo systemctl enable docker #Habilita el servicio de docker al iniciar siempre el pc
```

## ❓ Comandos informátivos

```bash
$docker --version #Muestra la versión de docker

$docker images #Muestra todas las imágenes descargadas

$docker ps #Muestra todos los contenedores en ejecución

$docker ps -a #Muestra un historial de contenedores ejecutados
```

## 💿 Comandos para gestionar imágenes

```bash
$docker search <imagen> #Busca una imágen en dockerhub

$docker pull <imagen> #Descarga una imágen desde dockerhub

$docker tag <imagen> <nombre:tag> #Etiqueta una imágen

$docker history <imagen> #Muestra el historial de cómo se creó la imágen
```

## 💼 Comandos para gestionar contenedores

```bash
$docker inspect <id-contenedor> #Muestra información del contenedor

$docker start <id-contenedor> #Inicia un contenedor creado con antelación

$docker stop <id-contenedor> #Detiene un contenedor

$docker rename <id-contenedor> <nombre> #Renombra el contenedor

$docker run <id-contenedor> #Inicia el contenedor y si no tiene la imágen, la descarga

$docker exec -d <id-contenedor> touch hola.php #Ejecuta un comando dentro de un contenedor ya iniciado, ejemplo con touch

$docker attach <id-contenedor> #Permite adjuntarse a un contenedor en ejecución y ver su salida

$docker logs <id-contenedor> #Permite ver los logs "registros" del contenedor
```

## 📂 Comandos para gestionar volumenes

```bash
$docker volume create <nombre> #Crea un volumen con cierto nombre

$docker volume inspect <nombre> #Muestra información del volumen

$docker volume ls #Lista todos los volumenes
```

## 🛀 Comandos para gestionar la limpieza en docker

```bash
$docker rmi <imagen> #Borra una imágen descargada

$docker rm <id-contenedor> #Elimina un contenedor si está detenido

$docker volume rm <nombre> #Elimina un volumen

$docker stop $(docker ps -q) #Detiene todos los contenedores en ejecución

$docker image prune #Elimina todas las imágenes no etiquetadas

$docker image prune -a #Elimina todas las imágenes, incluyendo las etiquetadas

$docker container prune #Elimina todos los contenedores detenidos

$docker volume prune #Elimina todos los volumenes sin utilizar no asociados a un contenedor

$docker network prune #Elimina todas las redes sin utilizar no asociadas a un contenedor

$docker system prune -a #Elimina TODO a la vez
```

## 📐 Docker Compose y Dockerfile

```bash
$docker build -t nombre:tag . #Contruye una imágen dándole un nombre a partir de un Dockerfile

$docker-compose ps #Lista los contenedores en ejecución por compose

$docker-compose ps -a #Lista todos contenedores de compose

$docker-compose up #Inicia el docker compose en primer plano

$docker-compose up -d #Inicia el docker compose en segundo plano

$docker-compose stop #Detiene el docker compose

$docker-compose down #Detiene y elimina los contenedores, redes y volumenes definidos en el yml

$docker-compose down -v #Hace lo mismo que el anterior pero también elimina los volumenes asociados a los contenedores
```

## 💾 Copias de seguridad

```bash
$docker save -o backup.tar <imagen:tag> #Guarda una copia de una imágen en formato .tar

$docker load -i backup.tar #Carga la copia a partir de un archivo tar

$docker commit -a "nombre" -m "descripción" <id-contenedor> <user/imagen:tag> #Permite crear una nueva imágen a partir de un contenedor
```