---
title: 'CH2 - ABM'
---
# 🚢 **Cheatsheet Docker**

## 🔧 **Comandos Básicos**

```bash
docker --version            # Verificar la versión de Docker
docker run hello-world      # Ejecutar un contenedor de prueba para verificar la configuración de Docker
usermod -aG docker [usuario]   # Añadir un usuario al grupo Docker (para no usar sudo)
```

---

## 🛠 **Instalación de Docker Compose**

```bash
apt install docker-compose  # Instalar Docker Compose
```

---

## 📦 **Gestión de Contenedores**

```bash
docker exec -d [contenedor] touch /tmp/example  # Ejecutar un comando dentro de un contenedor en ejecución
docker attach [contenedor]                     # Adjuntar a un contenedor en vivo
docker logs -f --since=5s [contenedor]         # Ver los logs del contenedor en tiempo real
docker stop $(docker ps -q)                    # Detener todos los contenedores en ejecución
docker rm [nombre_o_id_contenedor]             # Eliminar un contenedor específico
docker commit -a "Nombre" -m "Registro de cambios" [contenedor] [usuario/imagen:etiqueta]  # Guardar un contenedor como una nueva imagen
```

---

## 🖼 **Gestión de Imágenes**

```bash
docker pull alpine:latest                     # Descargar una imagen desde Docker Hub
docker history nginx                          # Ver el historial de capas de la imagen
docker rmi ubuntu:18.04                       # Eliminar una imagen específica
docker images                                 # Listar todas las imágenes de Docker
docker tag [imagen:etiqueta] [nueva_imagen:latest]  # Añadir una nueva etiqueta a una imagen existente
docker save -o backup_image.tar [imagen]      # Guardar una imagen en un archivo
docker load -i backup_image.tar               # Cargar una imagen desde un archivo
```

---

## 🌐 **Flujo de Trabajo en Docker Hub**

```bash
docker login                                  # Iniciar sesión en Docker Hub
docker push [usuario/repositorio:etiqueta]    # Subir una imagen a Docker Hub
```

---

## 💾 **Gestión de Volúmenes de Docker**

```bash
docker volume create [nombre_volumen]         # Crear un volumen de Docker
docker volume ls                              # Listar todos los volúmenes
docker volume inspect [nombre_volumen]        # Ver detalles de un volumen
docker volume rm [nombre_volumen]             # Eliminar un volumen específico
```

---

## ⚙ **Uso de Docker Compose**

```bash
docker-compose up -d                          # Iniciar contenedores en segundo plano
docker-compose stop                           # Detener los servicios en ejecución
docker-compose down                           # Detener y eliminar contenedores, redes, volúmenes
```

---

## 🧹 **Limpieza del Sistema**

```bash
docker system prune                           # Limpiar contenedores, imágenes, volúmenes y redes no utilizados
docker system prune -a                        # Limpiar agresivamente todos los contenedores detenidos e imágenes no utilizadas
```

---

## 📝 **Miscelánea**

```bash
docker ps                                     # Listar todos los contenedores en ejecución
```
