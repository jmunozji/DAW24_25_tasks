---

title: 'CH2 - David Maratrat Pons'

---
# 📝 UD2 - Docker Cheatsheet

> David Maratrat Pons

## 🐳 Instalación de Docker

| Comando                                                                                                         | Descripción                                                        |
|-----------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **🐧 Linux**                                                                                                    |                                                                    |
| `sudo apt-get remove docker docker-engine docker.io containerd runc`                                            | Elimina las versiones antiguas de docker para que no interfieran   |
| `sudo apt-get update`                                                                                           | Actualiza el índice de paquetes                                    |
| `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`          | Instala paquetes necesarios para poder usar repositorios con HTTPS |
| `curl -fsSL https://download.docker.com/linux/ubuntu/gpg \| sudo apt-key add -`                                 | Descarga la clave GPG de Docker CE y la añadimos                   |
| `sudo apt-key fingerprint 0EBFCD88`                                                                             | Comprueba que se ha añadido la clave                               |
| `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"` | Añades el repositorio de docker a nuestros repositorios            |
| `sudo apt-get update`                                                                                           | Actualiza repositorios                                             |
| `sudo apt-get install docker-ce docker-ce-cli containerd.io`                                                    | Instala Docker                                                     |
| `sudo usermod -aG docker $USER`                                                                                 | Añade el usuario actual al grupo docker para no usar sudo          |
| **🪟  Windows & 🍏 MacOS**                                                                                      |                                                                    |
| [https://docs.docker.com/get-started/get-docker/](https://docs.docker.com/get-started/get-docker/)              | URL de descarga de Docker                                          |

---

## 📁 Comandos básicos

| Comando               | Descripción                                                     |
|-----------------------|-----------------------------------------------------------------|
| `docker version`      | Permite ver la versión de docker instalada                      |
| `docker ps`           | Muestra una lista de los contenedores en ejecución              |
| `docker ps -a`        | Muestra una lista de todos los contenedores dentro del sistema  |

---

## 📦 Contenedores

| Comando                             | Descripción                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------|
| `docker run <imagen>`               | Crea y arranca un contenedor desde una imagen                                         |
| `docker run -it <imagen>`           | Crea y arranca un contenedor desde una imagen con una consola interactiva             |
| `docker start <id>`                 | Arranca un contenedor ya creado                                                       |
| `docker stop <id>`                  | Para un contenedor en ejecución                                                       |
| `docker restart <id>`               | Reinicia un contenedor en ejecución                                                   |
| `docker inspect <id>`               | Muestra información del contenedor                                                    |
| `docker exec <id>`                  | Permite ejecutar un comando dentro de un contenedor en ejecución                      |
| `docker cp <path> <path>`           | Permite copiar ficheros/directorios de la máquina anfitrión al contenedor y viceversa |
| `docker attach`                     | Conecta la consola al proceso en ejecución de un contenedor                           |
| `docker logs <id>`                  | Muestra los registros de salida de un contenedor                                      |
| `docker rename <id> <nuevo_nombre>` | Permite cambiar el nombre asociado a un contenedor                                    |
| `docker rm <id>`                    | Elimina un contenedor detenido                                                        |

---

## 🖼️ Imágenes

| Comando                                 | Descripción                                               |
|-----------------------------------------|-----------------------------------------------------------|
| `docker pull <imagen>`                  | Descarga una imagen desde Docker Hub                      |
| `docker build -t <nombre>`              | Construye una imagen a partir de un Dockerfile            |
| `docker history <imagen>`               | Muestra el historial de una imagen                        |
| `docker images`                         | Muestra todas las imágenes locales                        |
| `docker rmi <imagen>`                   | Elimina una imagen local                                  |
| `docker tag <imagen> <nombre:etiqueta>` | Etiqueta una imagen para que sea más fácil de identificar |
| `docker save -o <archivo> <imagen>`     | Guarda una imagen como un archivo tar                     |
| `docker load -i <archivo>`              | Carga una imagen desde un archivo tar                     |

---

## 📦 Volúmenes

| Comando                          | Descripción                                                           |
|----------------------------------|-----------------------------------------------------------------------|
| `docker volume create <name>`    | Crea un volumen                                                       |
| `docker volume ls`               | Lista todos los volúmenes disponibles                                 |
| `docker volume inspect <name>`   | Muestra información detallada de un volumen                           |
| `docker volume rm <name>`        | Elimina un volumen                                                    |
| `docker volume prune`            | Elimina todos los volúmenes que no estan en uso por ningún contenedor |
| `docker run -v <volumen>:<path>` | Monta un volumen en un contenedor durante su ejecución                |

---

## 📜 Compose

| Comando                                   | Descripción                                                              |
|-------------------------------------------|--------------------------------------------------------------------------|
| `docker-compose up`                       | Arranca todos los servicios definidos en el archivo `docker-compose.yml` |
| `docker-compose down`                     | Detiene y elimina los contenedores de `docker-compose.yml`               |
| `docker-compose build`                    | Construye o reconstruye los servicios                                    |
| `docker-compose logs`                     | Muestra los registros de salida de los servicios                         |
| `docker-compose ps`                       | Lista los contenedores administrados por Compose                         |
| `docker-compose exec <service> <command>` | Ejecuta un comando en un servicio en ejecución                           |

---

## 🌐 Docker HUB

| Comando                  | Descripción                                           |
|--------------------------|-------------------------------------------------------|
| `docker pull <imagen>`   | Descarga una imagen desde Docker Hub                  |
| `docker push <imagen>`   | Sube una imagen local a Docker Hub                    |
| `docker search <filtro>` | Busca imágenes en Docker Hub utilizando un filtro     |
| `docker login`           | Inicia sesión en Docker Hub para poder subir imágenes |
| `docker logout`          | Cierra sesión en Docker Hub                           |

---

## 🔧 Otros

| Comando                                                              | Descripción                                                                     |
|----------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `sudo systemctl enable/start/stop/disable/restart docker.service`    | Permite habilitar/iniciar/parar/deshabilitar/reiniciar el servicio de docker    |
| `sudo systemctl enable/start/stop/disable/restart container.service` | Permite habilitar/iniciar/parar/deshabilitar/reiniciar el servicio de container |
| `docker network ls`                                                  | Lista las redes creadas en Docker                                               |
| `docker network create <name>`                                       | Crea una red entre contenedores                                                 |
| `docker stats`                                                       | Muestra los recursos en tiempo real de los contenedores en ejecución            |
| `docker system prune`                                                | Limpia contenedores, redes y volúmenes no utilizados                            |

---

## ❌ Desinstalación de Docker (Linux)

| Comando                                                    | Descripción                   |
|------------------------------------------------------------|-------------------------------|
| `sudo apt-get purge docker-ce docker-ce-cli containerd.io` | Elimina docker del sistema    |
| `sudo rm -rf /var/lib/docker`                              | Borra el directorio de Docker |

