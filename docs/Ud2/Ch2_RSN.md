---
title: 'CH2 - RSN'
---

# CHEATSHEET COMANDOS DOCKER

## Instalación

- sudo docker version -> Se utiliza para comprobar la versión de docker y verificar su instalación.
- sudo usermod -aG $USER -> Permite utilizar docker sin la necesidad de sudo.
- sudo apt install docker-compose -> Instala la versión compose de docker.

## Principales acciones de Docker

### Docker run

- docker run -it --name=cont1 ubuntu /bin/bash -> Crea un contenedor con la imagen **Ubuntu**, le establece el nombre cont1 y lo lanza en modo terminal.
- docker run -d -p 1200:80 nginx -> Crea una imagen de **nginx** y lo lanza aen "background", exponiendo el puerto 80 del contenedor en el puerto 1200 del anfitrión.
- docker run -it -e MENSAJE=HOLA ubuntu:14.04 bash -> Crea la imagen **Ubuntu** con la versión 14.04 y establece la variable de entorno como "MENSAJE".

### Docker ps

- docker ps -> Muestra la información de los contenedores en **ejecución**.
- docker ps -a -> Muestra la información de todos los contenedores, **los parados y en ejecución**.

### Docker Start/Stop/Restart

- docker start "id" -> Arranca el contenedor
- docker start -ai "id" -> Arranca el contenedor, ejecutando el arranque a la **entrada** y **salida** estándar de la terminal.
- docker stop "id" -> Se utiliza para detener un contenedor en ejecución.
- docker restart "id" -> Reinicia el contenedor que se encuentra en ejecución.

### Docker exec

- docker exec -it -e FICHERO=prueba cont bash -> Lanza en el contenedor “cont” (que debe estar arrancado) el comando “bash”, estableciendo la variable de entorno 
“FICHERO” y enlazando la ejecución de forma interactiva a la entrada y salida estándar del anfitrión.
- docker exec -d cont touch /tmp/prueba -> Lanza en el contenedor “cont” (que debe estar arrancado) el comando “touch /tmp/prueba”. Este comando se
ejecuta en segundo plano, generando el fichero “/tmp/prueba”.


### Docker attach

- docker attach idcontainer -> Enlaza nuestra terminal la entrada/salida de nuestra al proceso en segundo plano del contenedor
“idcontainer”.

### Docker logs

- docker logs -n 10 idcontainer -> Muestra las 10 últimas líneas de la salida estandar producida por el proceso en ejecución en el contendor.

###Docker cp

- docker cp idcontainer:/tmp/prueba ./ -> Copia el fichero “/tmp/prueba” del contenedor “idcontainer” al directorio actual del anfitrión.
- docker cp ./miFichero idcontainer:/tmp -> Copia el fichero “miFichero” del directorio actual del anfitrión a la carpeta “/tmp” del contenedor.

## Imágenes Docker

### Gestión de imágenes

- docker images -> Información de las imágenes locales disponibles
- docker search ubuntu -> Busca la imagen “ubuntu” en el repositorio remoto (por defecto Docker Hub).Busca la imagen “ubuntu” en el repositorio remoto 
(por defecto Docker Hub).
- docker pull alpine -> Descarga localmente imagen “alpine”.
- docker history alpine -> Muestra la historia de creación de la imagen “alpine”.
- docker rmi ubuntu:14.04 -> Elimina localmente la imagen “ubuntu” con tag “14.04”.
- docker rmi $(docker images -q) -> Borra toda imagen local que no esté siendo usada por un contenedor.
- docker rm IDCONTENEDO ->  Borra un contenedor con IDCONTENEDOR.
- docker stop $(docker ps -a -q) -> Para todos los contenedores del sistema.
- docker rm $docker ps -a -q) -> Borra todos los contenedores parados del sistema.
- docker system prune -a -> Borra todas las imágenes y contenedores parados del sistema.

### Creación de imágenes a partir de contenedores

- docker commit -m “comentario” IDCONTENEDOR usuario/imagen:version -> Hace commit de un contenedor existente a una imagen local.
- docker save -o copiaSeguridad.tar imagenA -> Guarda una copia de seguridad de una imagen en fichero “.tar”.
- docker load -i copiaSeguridad.tar -> Restaura una copia de seguridad de una imagen en fichero “.tar”.

### Docker hub

- docker login -> Permite introducir credenciales del registro (por defecto “Docker Hub”).
- docker push usuario/imagen:version -> Permite subir al repositorio una imagen mediante “push”.

## Volúmenes Docker

- docker volume create wordpress-db -> Crea el volumen wordpress-db
- docker volume ls -> Lista todos los volúmenes
- docker volume inspect wordpress-db -> Visualiza el volúmen especificado
- docker volume rm wordpress-db -> Borra todo le volúmen

## Docker Compose

- docker-compose up -d -> Arranca la aplicación compose
- docker-compose stop -> Detiene los servicios
- docker-compose down -> Se borra el servicio
- docker-compose down -v -> Se borra el servicio y el volúmen
- docker-compose build/pull -> Construye/descarga las imágenes de contenedores según la configuración de “docker-compose.yml”.
- docker-compose ps -> Muestra información de los contenedores según la configuración de “docker-compose.yml”.
- docker-compose up -d --scale web=3 -> Similar a “docker-compose up -d” solo que además, el servicio definido como “web” en el fichero
“docker-compose.yml” lo escala creando 3 copias y realizando balanceo automático si se realiza una petición al
host llamado como el servicio “web”.



