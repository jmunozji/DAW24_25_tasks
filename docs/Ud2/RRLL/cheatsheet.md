---
title: 'CH2 - Rommel Romero Llumiquinga'
---

# Cheatsheet DOCKER de Rommel

## Instalación DOCKER

```bash
sudo apt-get update && sudo apt-get upgrade
```

Instalación de paquetes necesarios:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Añadir la clave GPG de Docker:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Añadir el repositorio de Docker:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Instalar Docker:

```bash
sudo apt update
sudo apt install docker-ce
```

Verificar la instalación:

```bash
docker --version
```

## Contenedores

### Crear y correr el contenedor

```bash
docker run [OPCIONES] nombre_imagen [COMANDO] [ARGUMENTO]
```

Opciones:

- -d -> En segundo plano

- -i -> Interactivo

- -e -> Pasar variable

- -t -> Lanza al arrancar el contenedor una pseudo terminal en el contenedor

- -rm -> Al detener el contenedor se elimina

- --name -> Para poner nombre al contenedor

- -p -> Publica puertos


### Crear el contenedor

```bash
docker create [OPCIONES] nombre_imagen [COMANDO] [ARGUMENTO]
```

Opciones:

- -e -> Pasar variable

- -rm -> Al detener el contenedor se elimina

- --name -> Para poner nombre al contenedor

- -p -> Publica puertos

### Arrancar un contenedor

```bash
docker start [OPCIONES] nombre_o_id_del_contenedor
```

Opciones:

- -i -> interactivo

- -a -> muestra la salida del contenedor

### Parar contenedor

```bash
docker stop nombre_o_id_del_contenedor
```

**Parar todos los contenedores**

```bash
docker stop $(docker ps -a -q)
```

### Ejecutar comandos en contenedores

```bash
docker exec [OPCIONES] nombre_o_id_del_contenedor COMANDO [ARGUMENTO]
```

Opciones:

- -i -> Interactivo

- -e -> Pasar variable

- -t -> Para tener una pseudo-terminal en el contenedor

- -d -> En segundo plano

### Mostrar contenedores

```bash
docker ps [OPCIONES]
```

Opciones:

- -a -> Muestra también los contenedores parados

- -f=ancestor="imagen" -> Filtra los contenedores basados en la imagen especificada

- -q -> Muestra solo los id

### Eliminar contenedores

```bash
docker rm nombre_o_id_del_contenedor
```

**Eliminar todos los contenedores**

```bash
docker rm $(docker ps -a -q)
```

### Información sobre el contenedor

```bash
docker inspect nombre_o_id_del_contenedor
```

### Copiar ficheros entre anfitrión y contenedor

**Anfitrión a contenedor**

```bash
docker cp ruta_origien idcontainer:ruta_destino
```

**Contenedor a anfitrión**

```bash
docker cp idcontainer:ruta_origien ruta_destino
```

### Acceder a un contenedor en ejecución

```bash
docker attach nombre_o_id_del_contenedor
```

### Registros de un contenedor

```bash
docker logs [OPCIONES] nombre_o_id_del_contenedor
```

Opciones:

- -f -> Muestra los registros en tiempo real

### Renombrar contenedores

```bash
docker rename nombre_o_id_del_contenedor nuevo-nombre
```

## Imágenes

### Ver todas las imágenes

```bash
docker images [OPCIONES]
```

Opciones:

- -q -> Muestra solo el id

- -f=reference="" -> Filtrar imágenes

### Listar imágenes que podemos descargar

```bash
docker search nombre_imagen
```

### Descargar imágenes

```bash
docker pull nombre_imagen
```

### Historial de una imagen

```bash
docker history nombre_imagen
```

### Eliminar imágenes

```bash
docker rmi nombre_imagen
```

**Eliminar todas las imágenes**

```bash
docker rmi $(docker images -q)
```

**Elimina toda imagen y contenedor parado**

```bash
docker system prune -a
```

### Crear imagen a partir de un contenedor

```bash
docker commit -a "autor" -m "comentario" id_o_nombre_contenedor usuario/nombreImagen[:version]
```

### Añadir un tag a la imagen

```bash
docker tag tag_imagen nuevo_tag
```

### Relizar una copia de la imagen

**Guardar**

```bash
docker save -o nombre_copia.tar imagen
```

**Cargar**

```bash
docker load -i copiaSeguridad.tar
```

## Dockerhub

### Conectarse a dockerhub

```bash
docker login
```

### Subir una imagen a dockerhub

```bash
docker push nombre_imagen
```

## Dockerfile

### Construir imagen a partir de un Dockerfile

```bash
docker build -t "nombre_imagen" ./
```

## Volumenes

### Crear volumen

```bash
docker volume create nombre_volumen
```

### Listar los volumenes creados

```bash
docker volume ls
```

### Mostrar información del volumen

```bash
docker volume inspect nombre_volumen
```

### Borrar volumen 

```bash
docker volume rm nombre_volumen
```

### Montar una carpeta del anfitrión en un contenedor

```bash
docker run -d -it --name nombre_contenedor --mount type=bind,source=/ruta/anfitrion,target=/ruta/contnedor
```

### Montar un volumen en un contenedor

```bash
docker run -d -it --name nombre_contenedor --mount type=volume,source=nombre_volumen,target=/ruta/contnedor imagen
```

## Docker compose

### Arrancar un compose

```bash
docker-compose up
```

**Arrancarlo en segundo plano**

```bash
docker-compose up -d
```

### Ver los contenedores que hemos hecho con compose

```bash
docker-compose ps
```

### Detener los contenedores que hemos hecho con compose

```bash
docker-compose stop
```

### Borrar los contenedores que hemos hecho con compose

```bash
docker-compose down
```

**Borrar los contenedores y volumenes**

```bash
docker-compose down -v
```
