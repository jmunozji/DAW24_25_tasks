---
title: 'CH2 - PPE'
---

# **Cheatsheet UD2 - Contenedores (Docker)**

Este documento resume los comandos para la creación y gestión de contenedores **Docker** en sistemas Ubuntu, correspondientes a la **Unidad 2** del curso de **Despliegue de Aplicaciones**.




## 🖥️ Instalación de Docker paso a paso (Linux)

La instalación varia dependiendo de la distribución. A continuación, se presenta solo la instalación en sistemas **Ubuntu**, aunque se puede consultar la [documentación oficial](https://docs.docker.com/engine/install) para instalar en otras versiones.

### 📦 Configurar el repositorio apt

| **🛠️ Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt update && sudo apt upgrade`              | Actualiza la lista y los paquetes instalados a la última versión disponible.                                 |
| `sudo apt-get install ca-certificates curl`        | Instala dependencias de docker.                                                                              |
| `sudo install -m 0755 -d /etc/apt/keyrings`        | Crea el directorio para almacenar la clave GPG con los permisos necesarios.                                  |
| `sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc` | Descarga la clave GPG de Docker                                  |
| `sudo chmod a+r /etc/apt/keyrings/docker.asc`      | Asigna permisos de lectura a la clave GPG.                                                                   |
| `sudo apt update`                                  | Actualiza la lista de paquetes para incluir el repositorio de Docker.                                        |

### 🐳 Instalar Docker

| **🛠️ Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin` | Instala los paquetes necesarios para crear contenedores Docker.                                                                                                                                                             |
| `sudo docker version`                              | Ver la versión de docker instalada en nuestro equipo.                                                        |
| `sudo docker run hello-world`                      | Ejecuta un contenedor de prueba para verificar que Docker se ha instalado correctamente.                     |

> **💡 Tip**: Una vez hemos terminado con la instalación de Docker y hayamos comprobado que funciona correctamente, podemos ejecutar el comando `sudo usermod -aG docker $USER`. De este modo, estamos permitiendo que nuestro usuario activo pueda ejecutar sentencias de docker sin necesidad de utilizar **sudo**.




## 🖥️ Acciones Principales

### 🏃 Docker Run

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker run -it --name=cont1 ubuntu /bin/bash`     | Crea y ejecuta un contenedor interactivo con Ubuntu y abre una sesión de `bash`.                              |
| `docker run -d -p 1200:80 apache2`                 | Ejecuta un contenedor en segundo plano y mapea el puerto `1200` del anfitrión al `80` del contenedor.         |
| `docker run -it -e MENSAJE=HOLA ubuntu:14.04 bash` | Crea y ejecuta un contenedor interactivo con la variable de entorno MENSAJE.                                  |

### 📋 Docker Ps

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker ps`                                        | Lista los contenedores en ejecución.                                                                          |
| `docker ps -a`                                     | Lista todos los contenedores, incluyendo los detenidos.                                                       |

### 🔄 Docker Start/Stop/Restart

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker start contenedor`                          | Inicia un contenedor detenido.                                                                                |
| `docker start -ai contenedor`                      | Inicia un contenedor, enlazando el comando a la entrada y salida del terminal del anfitrión.                  |
| `docker stop contenedor`                           | Detiene un contenedor en ejecución.                                                                           |
| `docker stop $(docker ps -a -q)`                   | Detiene todos los contenedores en ejecución.                                                                  |
| `docker restart contenedor`                        | Reinicia un contenedor en ejecución.                                                                          |

### 🌐 Docker Exec

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker exec -d ubuntu touch /tmp/prueba.txt`      | Ejecuta el comando `touch` dentro del contenedor en segundo plano.                                            |
| `docker exec -it -e VAR=1 ubuntu_pruebas bash`     | Ejecuta una sesión interactiva de `bash` en el contenedor con una variable de entorno.                        |

### 🔗 Docker Attach

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker attach contenedor`                         | Conecta el terminal del equipo anfitrión al del contenedor en ejecución.                                      |

### 📜 Docker Logs

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker logs contenedor`                           | Muestra los registros generados por el contenedor desde su inicio.                                            |
| `docker logs -f contenedor`                        | Sigue los registros del contenedor en tiempo real.                                                            |
| `docker logs -n 10 contenedor`                     | Muestra las 10 últimas líneas de los registros generado por el contenedor.                                    |

### 📂 Docker CP

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker cp contenedor:/tmp/prueba ./`              | Copia el fichero `/tmp/prueba` del contenedor al directorio actual del anfitrión.                             |
| `docker cp ./miFichero contenedor:/tmp`            | Copia el fichero `miFichero` del directorio actual del anfitrión a la carpeta `/tmp` del contenedor.          |

### 🗑️ Docker RM

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker rm contenedor`                             | Elimina el contenedor (necesita estar detenido previamente).                                                  |
| `docker rm $(docker ps -a -q)`                     | Elimina todos los contenedores detenidos.                                                                     |




## 🖥️ Gestión de Imágenes

### 🛠️ Secuencias Básicas

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker images`                                    | Lista todas las imágenes disponibles en el sistema.                                                           |
| `docker search ubuntu`                             | Busca imágenes de Docker Hub que coincidan con el término `ubuntu`.                                           |
| `docker pull ubuntu`                               | Descarga la imagen `ubuntu` desde Docker Hub.                                                                 |
| `docker history ubuntu`                            | Muestra el historial de capas de la imagen `ubuntu`.                                                          |
| `docker rmi ubuntu:14.04`                          | Elimina la imagen `ubuntu:14.04` del sistema (`14:04` indica la versión de la imagen).                        |
| `docker rmi $(docker images -q)`                   | Elimina todas las imágenes Docker del sistema.                                                                |

### 🏗️ Creación de Imágenes

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker commit -m "comentario" IDCONTENEDOR usuario/imagen:version` | Crea una nueva imagen a partir de un contenedor en ejecución.                                |
| `docker save -o copiaSeguridad.tar imagenA`        | Guarda una imagen Docker en un archivo comprimido como copia de seguridad.                                    |
| `docker load -i copiaSeguridad.tar`                | Carga una imagen Docker desde un archivo comprimido `tar`.                                                    |

### 🌐 Docker Hub

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker login`                                     | Inicia sesión en Docker Hub.                                                                                  |
| `docker push usuario/imagen:version`               | Sube una imagen al repositorio de Docker Hub.                                                                 |

### 📄 Dockerfile

El `Dockerfile` es un archivo de texto que contiene todas las instrucciones necesarias para construir una imagen Docker. Algunos comandos básicos son:

```dockerfile
# Usar una imagen base oficial de Node.js
FROM node:14

# Establecer el directorio de trabajo en el contenedor
WORKDIR /app

# Copiar el archivo package.json y package-lock.json al directorio de trabajo
COPY package*.json ./

# Instalar las dependencias del proyecto
RUN npm install

# Copiar el resto de los archivos de la aplicación al directorio de trabajo
COPY . .

# Exponer el puerto en el que la aplicación escuchará
EXPOSE 3000

# Comando para ejecutar la aplicación
CMD ["node", "app.js"]
```
> **💡 Tip**: Para ejecutar el fichero y construir la imagen, se utiliza el comando `docker build -t imagen .`, siendo `.` el directorio donde se encuentra el **Dockerfile**.




## 🖥️ Volúmenes

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                 |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| `docker volume create volumen`                     | Crea un nuevo volumen.                                                                                        |
| `docker volume ls`                                 | Lista todos los volúmenes Docker disponibles en el sistema.                                                   |
| `docker volume inspect volumen`                    | Inspecciona el volumen para ver sus detalles.                                                                 |
| `docker volume rm volumen`                         | Elimina el volumen.                                                                                           |
| `docker system prune -a`                           | Elimina todos los contenedores, imágenes y volúmenes no utilizados.                                           |
| `docker run -d -it --name appcontainer -v volumen:/app nginx:latest` | Ejecuta un contenedor con Nginx en segundo plano y monta el volumen `volumen` en `/app`.    |

### 🌐 Integración con contenedores

Estos comandos muestran cómo integrar volúmenes con contenedores Docker:

```bash
docker run -d --name wordpress-db \
    --mount source=vol-wordpress-db,target=/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9
```
- Crea y ejecuta un contenedor BBDD utilizando un volumen existente

```bash
docker run -d --name wordpress \
    --link wordpress-db:mysql \
    --mount type=bind,source="$(pwd)"/target,target=/var/www/html \
    -e WORDPRESS_DB_USER=manager \
    -e WORDPRESS_DB_PASSWORD=secret \
    -p 8080:80 \
    wordpress:4.9.8
```

- Crea y ejecuta un contenedor WordPress, conectandolo al contenedor anterior y montando un directorio del host




## 🖥️ Docker Compose

### 📄 Ejemplo básico de fichero docker-compose.yaml

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  db:
    image: mariadb:10.3.9
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: exampledb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
```

### 🛠️ Principales comandos

| **🛠️ Comando**                                     | **📝 Acción**                                                                                                  |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `docker-compose up -d`                             | Inicia todos los servicios definidos en el archivo `docker-compose.yaml` en segundo plano.                     |
| `docker-compose down`                              | Detiene y elimina todos los contenedores, redes y volúmenes creados por `docker-compose up`.                   |
| `docker-compose build`                             | Construye o reconstruye los servicios definidos en el archivo `docker-compose.yaml`.                           |
| `docker-compose pull`                              | Descarga las imágenes de los servicios definidos en el archivo `docker-compose.yaml`.                          |
| `docker-compose ps`                                | Lista los contenedores en ejecución definidos en el archivo `docker-compose.yaml`.                             |
