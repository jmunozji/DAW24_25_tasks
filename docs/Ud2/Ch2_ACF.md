---
title: 'Ch2 - ACF'
---

# 🐳 Docker Cheatsheet

### 1. 🛠️ Comandos de instalación y comprobación

| Comando | Explicación |
|----------|----------|
| `apt-get install docker-compose` | 🖥️ Comando para instalar Docker Compose en Ubuntu o Debian |
| `docker --version` | 🔍 Ver la versión de Docker instalada |
| `docker run hello-world` | 🚀 Comprobar el funcionamiento de Docker |
| `usermod -aG docker $USER` | 👤 Añadir usuario al grupo Docker |

### 2. 🚀 Principales acciones con Docker

| Comando | Explicación |
|----------|----------|
| `docker exec -d (contenedor) touch /tmp/prueba` | 🔧 Ejecutar un comando dentro de un contenedor |
| `docker attach (contenedor)` | 📎 Adjuntar a un contenedor en ejecución |
| `docker logs -f --until=2s (contenedor)` | 📜 Ver registros del contenedor |
| `docker ps` | 📋 Listar contenedores en ejecución |
| `docker start (contenedor)` | ▶️ Iniciar un contenedor |
| `docker stop (contenedor)` | ⏹️ Parar un contenedor |
| `docker rm (contenedor)` | 🗑️ Borrar un contenedor |
| `docker system prune -a` | 🧹 Eliminar todos los contenedores no utilizados |
| `docker network create (nombre-red)` | 🌐 Crear red compartida entre contenedores |

### 3. 🖼️ Comandos de Imágenes

| Comando | Explicación |
|----------|----------|
| `docker images` | 📂 Ver imágenes almacenadas |
| `docker search (imagen)` | 🔎 Buscar información sobre una imagen |
| `docker pull (imagen)` | ⬇️ Descargar una imagen localmente |
| `docker history (imagen)` | 📜 Ver historial de una imagen |
| `docker rmi (imagen)` | 🗑️ Eliminar una imagen almacenada |
| `docker tag` | 🏷️ Etiquetar una imagen |
| `docker save` | 💾 Guardar una copia de una imagen |
| `docker load` | 📥 Importar archivo para crear una imagen |
| `docker commit -m “comentario” IDCONTENEDOR usuario/imagen:version` | 📸 Crear una imagen de un contenedor existente |
| `docker build -t ubuntunano ./` | 🛠️ Crear una imagen desde un Dockerfile |

### 4. 📂 Comandos de Volúmenes

| Comando | Explicación |
|----------|----------|
| `docker volume create (nombre-volumen)` | 🆕 Crear un volumen |
| `docker volume ls` | 📋 Listar volúmenes |
| `docker volume inspect (nombre-volumen)` | 🔍 Ver metadatos de un volumen |
| `docker volume rm (nombre-volumen)` | 🗑️ Borrar un volumen |

### 5. 🧩 Comandos Docker Compose

| Comando | Explicación |
|----------|----------|
| `docker-compose up -d` | 🚀 Iniciar contenedores en segundo plano |
