---
title: 'CH2 - RRLL'
---

# Cheatsheet DOCKER de Rommel

## Instalación

Actualizar los paquetes existentes:

```bash
    sudo apt update
    sudo apt upgrade
```

Instalar paquetes necesarios para Docker:

```bash
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Agregar la clave GPG de Docker:

```bash
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Agregar el repositorio de Docker:

```bash
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Actualizar de nuevo el índice de paquetes e instalar Docker:

```bash
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io
```

Verificar que Docker se haya instalado correctamente:

```bash
    sudo docker --version
```

## Contenedores

### Docker run

```bash
    docker run -it --name=mi_contenedor ubuntu /bin/bash
```

Crea un contenedor interactivo y inicia la terminar `bash` al ser ejecutado.

### Docker pa

```bash
    docker ps
```

Muestra los contenedores que están en ejecución.

```bash
    docker ps -a
```

Muestra los contenedores que están en ejecución y los que no (detenidos).

### Docker start

```bash
    docker start contenedor
```

Inicia un contenedor

```bash
    docker start -ai contenedor
```

Inicia un contenedor y nos permite ver e interactuar con su salida y entrada.

### Docker stop

```bash
    docker stop contenedor
```

Detiene el contenedor.

```bash
    docker stop $(docker ps -a -q)
```

Detiene todos los contenedores.

### Docker restart

```bash
    docker restart contenedor
```

Reinicia el contenedor.

### Docker exec

```bash
    docker exec ubuntu /bin/bash
```

Ejecuta el comando que pongas en el contenedor, aquí inicia un bash en un contenedor ubuntu, el contenedor tiene que estar iniciado.

### Docker attach

```bash
    docker attach contenedor
```

Conecta el terminal del host con el del contenedor.

### Docker logs

```bash
    docker logs contenedor
```

Muestra los registros generados por el contenedor.

```bash
    docker logs -f contenedor
```

Muestra los registros generados por el contenedor en tiempo real.

### Docker cp

```bash
    docker cp contenedor:ruta/archivo ruta/destino/host
```

Copia un archivo del contenedor al host.

```bash
    docker cp ruta/archivo/host contenedor:ruta/destino/contenedor
```

Copia un archivo del host al contenedor.

### Docker rm

```bash
    docker rm contenedor
```

Elimina el contenedor, tiene que estar parado para eliminarlo.

```bash
    docker rm $(docker ps -a -q)
```

Elimina todos los contenedores detenidos.

## Imágenes

### Docker images

```bash
    docker images
```

Lista todas las imágenes

### Docker search

```bash
    docker search nombre_imagen
```

Busca todas las imágenes en `Docker hub` que coincidan con el nombre que de imagen que hemos puesto.

### Docker pull

```bash
    docker pull nombre_imagen
```

Descarga la imagen que hemos especificado.

### Docker history

```bash
    docker history nombre_imagen
```

Muestra el historial de la imagen, es decir, todas las capas que la componen.

### Docker rmi

```bash
    docker rmi nombre_imagen
```

Elimina la imagen especificada.

```bash
    docker rmi $(docker images -q)
```

Elimina todas las imágenes.

### Docker commit

```bash
    docker commit -m "comentario" IDCONTENEDOR usuario/imagen:version
```

Crea una imagen a partir de un contenedor.

### Docker save

```bash
    docker save -o copiaSeguridad.tar nombre_imagen
```

Guarda una imagen en una archivo comprimido.

### Docker load

```bash
    docker save -i copiaSeguridad.tar
```

Carga una imagen comprimida en un archivo `.tar`.

## Dockerhub

### Inicio sesión

```bash
    docker login
```

Inicia sesion en `Docker Hub`.

### Subir imagen

```bash
    docker push usuario/imagen:[version]
```

Sube la imagen a `Docker hub`

## Dockerfile

### Archivo Dockerfile

```dockerfile
    FROM nombre_imagen # Definir la imagen que vamos a usar

    WORKDIR /app # Establece el directorio de trabajo

    COPY <origen> <destino>  # Copia archivos del host al contenedor

    ADD <origen> <destino>  # Copia archivos y los descomprime si es necesario

    RUN apt-get update && apt-get install -y <paquete>  # Ejecuta un comando durante la construcción

    CMD ["executable", "param1", "param2"]  # Comando por defecto al iniciar el contenedor

    ENTRYPOINT ["executable", "param1"]  # Comando fijo que siempre se ejecuta al iniciar el contenedor

    EXPOSE 8080  # Expone el puerto 8080

    ENV VARIABLE=value  # Establece una variable de entorno

    VOLUME ["/data"]  # Crea un volumen en /data

```

### docker build

```bash
    docker build -t imagen .
```

## Volumenes

### Crear volumenes

```bash
    docker volume create nombre_volumen
```

Crea un volumen.

### Listar volumenes

```bash
    docker volume ls
```

Lista todos los volumenes.

### Inspeccionar volumen

```bash
    docker volume inspect nombre_volumen
```

Inspecciona el volumen.

### Eliminar volumen

```bash
    docker volume rm nombre_volumen
```

Eliminar volumen.

### Eliminar volúmenes, imágenes y contenedores no usados

```bash
    docker system prune -a
```

Elimina todos los volúmenes, imágenes y contenedores no usados.

### Integrar con contenedores

Para integrar un volumen a un contenedor hacemos lo siguiente:

```bash
    docker run -d --name wordpress-db \
    --mount type=volume,source=vol-wordpress-db,target=/var/lib/mysql \  # integración del volumen
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9
```

Para montar una carpeta hacemos lo mismo cambiando el `type` por `bind`:

```bash
    docker run -d --name wordpress-db \
    --mount type=bind,source=/ruta/en/host,target=/var/lib/mysql \ # integración de la carpeta
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9
```

## Docker-Compose

### Archivo docker-compose.yaml

```bash

version: '3.8'  # Define la versión de Docker Compose

services:
  apache:
    image: debian:latest  # Usamos la imagen oficial de Debian
    container_name: apache2  # Nombre del contenedor
    build:
      context: .  # Utilizamos el Dockerfile en el directorio actual para construir la imagen
    volumes:
      - ./html:/var/www/html  # Montamos la carpeta local 'html' al directorio /var/www/html dentro del contenedor
    ports:
      - "8080:80"  # Mapeamos el puerto 8080 del host al puerto 80 del contenedor
    command: bash -c "apt update && apt install -y apache2 && apache2ctl -D FOREGROUND"  # Instalamos Apache2 y lo arrancamos

```

### Comandos principales

Iniciar los servicios del `docker-compose`:

```bash
    docker-compose up
```

Iniciar los servicios en segundo plano:

```bash
    docker-compose up
```

Detener y eliminar los servicios del `docker-compose`:

```bash
    docker-compose down
```

Para construir los servicios del `docker-compose`:

```bash
    docker-compose build
```

Para mostrar los contenedores gestionados por `docker-compose`:

```bash
    docker-compose ps
```

Para descargar los imágenes de los servicios definidos en el `docker-compose`:

```bash
    docker-compose pull
```
