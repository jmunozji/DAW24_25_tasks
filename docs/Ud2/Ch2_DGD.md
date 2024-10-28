---
title: 'Cheatsheet 2'
---

# Cheatsheet 2 Docker Darius Gabriel Dobre

Administrar **DOCKER** sin privilegios

```bash
sudo usermod -aG docker $USER
```


## 🎞️ Comandos para imágenes

| Comando | Descripción |
| ------- | ----------- |
| `docker images`| Ver imágenes locales. |
| `docker pull <imagen>` | Descargar imagen desde Docker Hub. |
| `docker push <nombre_imagen>` | Subir imagen a Docker Hub. |
| `docker build -t <nombre_imagen> <ruta_dockerfile>` | Construir imagen a partir de Dockerfile. |
| `docker rmi <nombre_imagen>` | Borrar imagen. |
| `docker history <nombre_imagen>` | Ver historial de la imagen. |
| `docker inspect <nombre_imagen>` | Ver detalle de la imagen. |

### Ejemplos para imágenes

- Borrar todas las imágenes:

    ```bash
    docker rmi $(docker images -q)
    ```


- Crear imagen a partir de contenedor

    ```bash
    docker commit servernginx nginx
    ```

- Exportar imagen

    ```bash
    docker save -o copiaseguridad.tar darius/ubuntunano
    ```

- Importar imagen

    ```bash
    docker load -i copiaseguridad.tar
    ```



## 🚢 Comandos para contenedores

| Comando | Descripción |
| ------- | ----------- |
| `docker ps` | Mostrar contenedores en ejecución. |
| `docker start <nombre_contenedor>` | Iniciar contenedor. |
| `docker stop <nombre_contenedor>` | Detener contenedor en ejecución. |
| `docker rm <nombre_contenedor>` | Borrar contenedor. |
| `docker inspect <nombre_contenedor>` | Ver detalles del contenedor. |
| `docker run` | Crear y ejecutar un contenedor. |
| `docker container prune` | Eliminar contenedores detenidos |

### Ejemplos para contenedores

- Crear un contenedor interactivo:

    ```bash
    docker run -it ubuntu bash
    ```

- Contenedor en segundo plano:

    ```bash
    docker run -d nginx
    ```

- Ejecutar comandos en contenedor en ejecucion:
    
    ```bash
    docker exec -it ubuntu bash
    ```

- Detener todos los contenedores en ejecucion:

    ```bash
    docker stop $(docker ps -q)
    ```

## 🗄️ Comandos para Volúmenes

| Comando                                            | Descripción                                 |
|---------------------------------------------------|---------------------------------------------|
| `docker volume create <nombre_volumen>`           | Crear un nuevo volumen.                     |
| `docker volume ls`                                | Listar todos los volúmenes.                |
| `docker volume inspect <nombre_volumen>`          | Ver detalles de un volumen específico.     |
| `docker volume rm <nombre_volumen>`               | Borrar un volumen.                          |
| `docker volume prune`                              | Eliminar todos los volúmenes no utilizados. |
| `docker run -v <nombre_volumen>:<ruta_en_contenedor> <imagen>` | Montar un volumen en un contenedor. |



### Ejemplos para Volúmenes

- Crear un volumen:

    ```bash
    docker volume create mi_volumen
    ```

- Montar un volumen en un contenedor:

    ```bash
    docker run -d -v mi_volumen:/data nginx
    ```

- Inspeccionar un volumen:

    ```bash
    docker volume inspect mi_volumen
    ```

- Borrar un volumen:

    ```bash
    docker volume rm mi_volumen
    ```

- Eliminar todos los volúmenes no utilizados:

    ```bash
    docker volume prune
    ```


## ⚙️ Otras opciones

| Comando | Descripción |
| --- | --- |
| `docker network` | Comandos para administrar redes de contenedores. |
| `docker system prune` | Elimina recursos no utilizados de Docker. |
| `docker-compose up -d` |  Arrancar aplicacion con Compose. |
| `docker-compose down` | Detener y eliminar contenedores. |
| `docker-compose stop` | Detener contenedores. |
| `docker-compose down -v` | Borra tambien volúmenes. |
