---

title: 'CH2 - VATC'

---

# 🌟 UD2 - Cheatsheet

> ✍️ **Victor Alexandru Teleanu Covaci**

## 🚀 Instalación y Configuración de Docker

### 🔧 **Instalación en diferentes sistemas operativos**

| **💻 Sistema Operativo / 🛠️ Comando**                                                     | **📝 Descripción**                                 |
|--------------------------------------------------------------------------------------------|----------------------------------------------------|
| **🐧 Linux (Debian/Ubuntu)**                                                               |                                                    |
| `sudo apt-get update && sudo apt-get upgrade`                                              | Actualizar paquetes antes de instalar Docker       |
| `sudo apt install docker-ce`                                                               | Instalar Docker Community Edition en Debian/Ubuntu |
| **🪟 Windows**                                                                             |                                                    |
| [🔗 Instrucciones para Windows](https://docs.docker.com/desktop/install/windows-install/ ) | Instalación de Docker Desktop para Windows         |
| **🍏 MacOS**                                                                               |                                                    |
| [🔗 Instrucciones para MacOS](https://docs.docker.com/desktop/install/mac-install/)        | Instalación de Docker Desktop para MacOS           |

### 🔍 **Comprobación de instalación y prueba**

- Con `sudo docker version` podemos verificar la instalación.
- Para probar, ejecutamos `sudo docker run hello-world`. Esto ejecutará un contenedor a partir de la imagen "hello-world".

## ⚙️ Configuración Básica de Docker

### 🧑‍💻 **Configurar usuario sin sudo**

| **🛠️ Comando**                   | **📝 Descripción**                  |
|-----------------------------------|-------------------------------------|
| `sudo usermod -aG docker $USER`   | Añade el usuario al grupo "docker"  |

### 🧩 **Configuración de Docker Compose**

- Con `sudo apt install docker-compose` instalamos Docker Compose en Debian/Ubuntu.
- Con `docker-compose --version` verificamos la versión instalada.

## 📚 Comandos Básicos de Docker

### 🚀 **Creación y gestión de contenedores**

| **🛠️ Comando**                                        | **📝 Descripción**                                |
|--------------------------------------------------------|---------------------------------------------------|
| `docker run`                                           | Crea y arranca un contenedor                      |
| `docker run -it --name=mi_contenedor ubuntu /bin/bash` | Crear y acceder a un contenedor con Ubuntu        |
| `docker exec -it mi_contenedor bash`                   | Ejecutar un comando en un contenedor en ejecución |
| `docker stop mi_contenedor`                            | Detiene un contenedor                             |
| `docker start mi_contenedor`                           | Inicia un contenedor detenido                     |
| `docker restart mi_contenedor`                         | Reinicia un contenedor                            |
| `docker rm mi_contenedor`                              | Elimina un contenedor detenido                    |

### 📂 **Gestión de Imágenes**

| **🛠️ Comando**                     | **📝 Descripción**                               |
|-------------------------------------|--------------------------------------------------|
| `docker images`                     | Muestra todas las imágenes disponibles           |
| `docker rmi nombre_imagen`          | Elimina una imagen de Docker                     |
| `docker pull nombre_imagen`         | Descarga una imagen del repositorio de Docker    |
| `docker build -t nombre_imagen .`   | Crea una imagen a partir de un Dockerfile        |
| `docker tag imagen nombre:etiqueta` | Etiqueta una imagen para identificarla más fácil |
### 📑 **Información**

| **🛠️ Comando**                  | **📝 Descripción**                                |
|----------------------------------|---------------------------------------------------|
| `docker ps`                      | Muestra los contenedores en ejecución             |
| `docker ps -a`                   | Muestra todos los contenedores, en ejecución o no |
| `docker inspect nombre`          | Detalla la configuración del contenedor           |
| `docker logs nombre_contenedor`  | Muestra los logs de un contenedor en ejecución    |

## 📦 Gestión de Volúmenes

| **🛠️ Comando**                        | **📝 Descripción**                              |
|----------------------------------------|-------------------------------------------------| 
| `docker volume create nombre_volumen`  | Crea un volumen                                 |
| `docker volume ls`                     | Lista todos los volúmenes existentes            |
| `docker volume rm nombre_volumen`      | Elimina un volumen si no está en uso            |
| `docker volume inspect nombre_volumen` | Muestra información detallada sobre un volumen  |
| `docker volume prune`                  | Elimina todos los volúmenes no utilizados       |

## 🌐 Redes en Docker

| **🛠️ Comando**                                   | **📝 Descripción**                      |
|---------------------------------------------------|-----------------------------------------|
| `docker network create nombre_red`                | Crea una red personalizada              |
| `docker network ls`                               | Lista todas las redes                   |
| `docker network inspect nombre_red`               | Muestra detalles de la red especificada |
| `docker network connect nombre_red contenedor`    | Conecta un contenedor a una red         |
| `docker network disconnect nombre_red contenedor` | Desconecta un contenedor de una red     |

## 🧑‍💻 Docker Compose

| **🛠️ Comando**          | **📝 Descripción**                       |
|--------------------------|------------------------------------------|
| `docker-compose up -d`   | Arranca la aplicación con Docker Compose |
| `docker-compose down`    | Borra servicios                          |
| `docker-compose ps`      | Muestra información de los contenedores  |
| `docker-compose logs -f` | Muestra los registros                    |
| `docker-compose stop`    | Detiene el servicio                      |
| `docker-compose start`   | Inicia el servicio                       |
| `docker-compose restart` | Reinicia los servicios                   |

Estos comandos dependen de los servicios que estén definidos en "docker-compose.yaml"

## 🛠️ Mantenimiento y Limpieza

| **🛠️ Comando**                                              | **📝 Descripción**                                 |
|--------------------------------------------------------------|----------------------------------------------------|
| `docker system prune`                                        | Borra contenedores, imágenes y redes sin uso       |
| `docker volume rm $(docker volume ls -q -f "dangling=true")` | Elimina volúmenes no asociados a ningún contenedor |
| `docker rm $(docker ps -q -f "status=exited") `              | Borra contenedores que han finalizado su ejecución |
| `docker rmi $(docker images -q -f "dangling=true") `         | Borra imágenes no etiquetadas y sin uso            |

## 📂 Copias de Seguridad

| **🛠️ Comando**                                                                                         | **📝 Descripción**                              |
|---------------------------------------------------------------------------------------------------------|-------------------------------------------------|
| `docker run --rm -v /tmp:/backup --volumes-from contenedor busybox tar -cvf /backup/backup.tar /data`   | Realiza un backup de los datos de un contenedor |
| `docker run --rm -v /tmp:/backup --volumes-from contenedor busybox tar -xvf /backup/backup.tar /data`   | Restaura un backup de los datos al contenedor   |
