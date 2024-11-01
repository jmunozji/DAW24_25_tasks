---
title: 'Ch2 - RPE'
---

# **Cheatsheet Contenedores Docker** 🐋

## 1. **Instalación** 🔧

### **Instalación en Linux**

| 🛠️ Paso                           | 📜 Comando                                                                                                          |
|------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| 📥 Incluir el repositorio          | ```sudo apt update```                                                                                               |
| ➡️ Instalar dependencias           | ```sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common```          |
| 🔑 Descargar clave GPG e incluirla | ```curl -fsSL https://download.docker.com/linux/ubuntu/gpg / sudo apt-key add -```                                  |
| ➕ Añadir el repositorio            | ```sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"``` |
| 🐋 Instalar Docker Engine CE       | ```sudo apt-get install docker-ce docker-ce-cli containerd.io```                                                    |
| 🐋 Instalar Docker Compose         | ```sudo apt install docker-compose```                                                                               |
| 👤 Uso sin privilegios             | ```sudo usermod -aG docker $USER```                                                                                 |
| 🗑️ Desinstalar Docker             | ```sudo apt-get purge docker-ce docker-ce-cli containerd.io```                                                      |

---

## 2. **Imágenes** 🖼️

| Acción                  | Comando                                         |
|-------------------------|-------------------------------------------------|
| 📜 Listar imágenes      | ```docker images```                            |
| 📥 Descargar imagen     | ```docker pull <nombre_imagen>```              |
| 🗑️ Eliminar imagen      | ```docker rmi <nombre_imagen>```               |
| 🏗️ Crear imagen         | ```docker build -t <nombre_imagen>:<tag> <directorio_dockerfile>``` |

---

## 3. **Contenedores** 📦

| Acción                               | Comando                                                      |
|--------------------------------------|--------------------------------------------------------------|
| ▶️ Ejecutar contenedor nuevo         | ```docker run <nombre_imagen>```                             |
| ▶️ Ejecutar en segundo plano         | ```docker run -d <nombre_imagen>```                          |
| 🖥️ Modo interactivo                  | ```docker run -it <nombre_imagen>```                         |
| 🔌 Asignar puerto                    | ```docker run -p <puerto_local>:<puerto_contenedor> <nombre_imagen>``` |
| 📜 Listar contenedores activos       | ```docker ps```                                              |
| 📜 Listar todos los contenedores     | ```docker ps -a```                                         |
| 🕵️ Inspeccionar contenedor           | ```docker inspect <id_contenedor>```                        |
| 📝 Mostrar logs                      | ```docker logs <id_contenedor>```                           |
| 🔄 Iniciar contenedor detenido       | ```docker start <nombre_contenedor>```                      |
| ⏹️ Detener contenedor                | ```docker stop <nombre_contenedor>```                       |
| 🗑️ Eliminar contenedor               | ```docker rm <id_contenedor>```                             |
| ⌨️ Ejecutar comando en contenedor    | ```docker exec -it <nombre_contenedor> <comando>```        |
| 🔄 Reiniciar contenedor              | ```docker restart <nombre_contenedor>```                   |

---

## 4. **Volúmenes** 📂

| Acción                              | Comando                                                      |
|-------------------------------------|--------------------------------------------------------------|
| ➕ Crear volumen                     | ```docker volume create <nombre_volumen>```                  |
| 📂 Montar volumen                   | ```docker run -v <nombre_volumen>:<directorio_contenedor> <nombre_imagen>``` |
| 📜 Listar volúmenes                 | ```docker volume ls```                                       |
| 🔍 Inspeccionar volumen             | ```docker volume inspect <nombre_volumen>```                |
| 🗑️ Eliminar volumen                 | ```docker volume rm <nombre_volumen>```                     |

---

## 5. **Redes en Docker** 🌐

| Acción                               | Comando                                            |
|--------------------------------------|----------------------------------------------------|
| 📜 Listar redes                      | ```docker network ls```                            |
| ➕ Crear red                          | ```docker network create <nombre_red>```          |
| 🗑️ Eliminar red                      | ```docker network rm <nombre_red>```               |
| 🔌 Conectar contenedor a red         | ```docker run --network <nombre_red> <nombre_imagen>``` |

---

## 6. **Docker Compose** 📋

| Acción                                    | Comando                                 |
|-------------------------------------------|-----------------------------------------|
| ▶️ Iniciar servicios                      | ```docker-compose up```                 |
| ▶️ Iniciar en segundo plano               | ```docker-compose up -d```              |
| 🔄 Reconstruir y reiniciar servicios      | ```docker-compose up --build```         |
| ⏹️ Detener servicios                     | ```docker-compose stop```                |
| 🗑️ Eliminar servicios                    | ```docker-compose down```                |
| 🗑️ Eliminar servicios y volúmenes        | ```docker-compose down -v```            |
| 📜 Ver logs de servicios                  | ```docker-compose logs```                |

---

## 7. **Limpieza** 🧹

| Acción                             | Comando                       |
|------------------------------------|-------------------------------|
| 🗑️ Eliminar objetos sin uso       | ```docker system prune```     |
| 🗑️ Eliminar contenedores parados  | ```docker container prune```  |
| 🗑️ Eliminar imágenes no usadas    | ```docker image prune```      |
| 🗑️ Eliminar volúmenes no usados   | ```docker volume prune```     |

---