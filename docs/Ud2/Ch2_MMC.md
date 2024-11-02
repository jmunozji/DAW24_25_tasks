---
title: 'CH2 - MMC'
---

# CheatSheet Docker MMC

Tipo: Apuntes
Estado: No empezado
Tema: Despliegue

## Post Instalación ⚙️

| sudo usermod -aG docker $USER | Añade tu usuario al grupo de docker. |
| --- | --- |
| sudo apt install docker-compose | Instalación de Docker Compose |

## Comandos Básicos 🗝️

| docker run <nombre_contenedor> |  Crea y arranca un controlador |
| --- | --- |
| docker ps | Listado de contenedores en ejecución |
| docker ps -a | Listado de todos los contenedores |
| docker start <nombre_contenedor> | Inicia un contenedor |
| docker stop <nombre_contenedor> | Detiene de manera ordenada un contenedor |
| docker restart <nombre_contenedor> | Reinicia un contenedor |
| docker rename contenedor1 contenedor2 | Cambiar el nombre asociado a un contenedor. |
| docker rm <nombre_contenedor> | Elimina un contenedor (solo se puede borrar si está parado) |
| docker rm $(docker ps -a -q) | Elimina todos los contenedores |

## Consultar información 🔍

| docker inspect <nombre_contenedor> | Proporciona diversos detalles de la configuración de un contenedor |
| --- | --- |
| docker logs  [OPCIONES] <nombre_contenedor> | Consultar la información de la salida estándar y de error |

## Movimientos en contenedores 🪩

| docker exec  [OPCIONES] <nombre_contenedor> COMANDO [ARGUMENTOS] | Permite ejecutar un comando dentro de un contenedor |
| --- | --- |
| docker cp idcontainer:/tmp/prueba ./ | Permite copiar ficheros y directorios del anfitriona un contenedor o viceversa. |
| docker attach  [OPCIONES] <nombre_contenedor> | Enlazar la entrada o salida estándar del terminal a un contenedor |

## Trato de imágenes 📺

| docker images | Listado de imágenes almacenadas |
| --- | --- |
| docker search <nombre_imagen> | Obtener información de imágenes descargables |
| docker pull <nombre_imagen> | Obtener imágenes sin ponerlo en un contenedor |
| docker history <nombre_imagen> | Histórico de una imagen descargada |
| docker rmi <nombre_imagen> | Elimina la imagen localmente |
| docker rmi $(docker images -q) | Elimina todas las imágenes locales |
| docker system prune -a | Elimina todas las imágenes y contenedores |
| docker commit -a "autor" -m "comentario" <nombre_imagen>
usuario/imagen:[version] | Crear una propia imagen |
| docker tag <nombre_imagen>
usuario/imagen:[version] | Añadir una etiqueta a una imagen |
| docker save -o copiaSeguridad.tar sergi/ubuntumod | Copia de seguridad, exportar |
| docker load -i copiaSeguridad.tar | Importar una imagen exportada previamente |
| docker import [http://ejemplo.com/mi-imagen.tar](http://ejemplo.com/mi-imagen.tar) | Crea una nueva imagen a partir del enlace |

## DockerHub 🐳

| docker login | Loguearnos al repositorio remoto |
| --- | --- |
| docker push  | Subir imágenes al repositorio remoto |

## DockerFile 📄

| docker build -t ubuntunano ./ | Crea una imagen a partir de un DockerFile |
| --- | --- |

### Comandos dentro del DockerFile 📝

| **FROM** ubuntu:latest | Define la imagen base desde la cual construir la imagen |
| --- | --- |
| **RUN** apt update | Ejecuta el comando escrito |
| **CMD** /bin/bash | Define un comando predeterminado que se ejecutará cuando el contenedor inicie |
| **EXPOSE** 80 443 8080 | Expone los puertos determinados |
| **ADD** ./mifichero.zip /var/www/html | Copia el archivo y lo descomprime si es un archivo comprimido. |
| **COPY** ./mifichero.zip /var/www/html | Copia el archivo pero sin descomprimirlo |
| **ENTRYPOINT** ["cat"] | Define el comando que siempre se ejecutará al iniciar el contenedor |
| **USER** sergi | Establece un usuario como el usuario predeterminado |
| **WORKDIR** /root | Define el directorio de trabajo predeterminado |
| **ENV** v1=”valor1” v2=”valor2” | Define variables de entorno con sus variables |
| **ARG** version=1.0

 | Define variables de construcción |
| **VOLUME** /data | Crea un volumen donde se almacenan datos persistentes |
| **LABEL** maintainer="tu_email@example.com" | Añade metadatos a la imagen |
| **HEALTHCHECK** CMD curl --fail http://localhost || exit 1 | Define un comando para verificar la salud del contenedor |

## Volumen 🔊

| docker volume create <nombre_volumen> | Crear volumen |
| --- | --- |
| docker volume ls | Listar volúmenes |
| docker volume inspect <nombre_volumen> | Ver metadatos del volumen |
| docker volume rm <nombre_volumen> | Borrar volumen |
| docker run -d --name wordpress-db \
    --mount source=vol-wordpress-db,target=/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9 | Ejemplo de crear un contenedor con un volumen específico |

## Docker Compose 📚

| docker-compose up -d | Iniciar los servicios definidos en un archivo docker-compose.yml en segundo plano |
| --- | --- |
| docker-compose ps | Listar los contenedores |
| docker-compose stop | Detener servicios |
| docker-compose down | Borrar servicios pero no volúmenes |
| docker-compose down -v | Borrar tanto contenedores como volúmenes |