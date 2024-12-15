---
title: 'Ch2 - ALS'
---


# 🐳 **Docker Cheatsheet**  
Una guía rápida para dominar los comandos esenciales de Docker.  


---

## 🔧 **1. Configurar Docker sin privilegios de root**  
Habilita Docker para tu usuario:  
```bash
sudo usermod -aG docker $USER
```

---

![docker-banner](https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png)  

---

## 🚢 **2. Comandos para Contenedores**

| 🛠️ **Comando**                               | 📝 **Descripción**                                    |
|---------------------------------------------|-----------------------------------------------------|
| `docker ps`                                 | Listar contenedores en ejecución.                   |
| `docker stop <contenedor>`           | Detener un contenedor en ejecución.                |
| `docker start <contenedor>`          | Iniciar un contenedor detenido.                    |
| `docker inspect <contenedor>`        | Ver detalles de un contenedor.                     |
| `docker run`                                | Crear y ejecutar un contenedor.                    |
| `docker rm <contenedor>`             | Eliminar un contenedor.                            |
| `docker container prune`                    | Eliminar contenedores detenidos.                   |

### 🌟 **Ejemplos Prácticos**  
- **Crear un contenedor interactivo**  
  ```bash
  docker run -it ubuntu bash
  ```  

- **Ejecutar un contenedor en segundo plano**  
  ```bash
  docker run -d nginx
  ```

- **Ejecutar un comando en un contenedor activo**  
  ```bash
  docker exec -it <contenedor> bash
  ```

- **Detener todos los contenedores activos**  
  ```bash
  docker stop $(docker ps -q -a)
  ```

---

## 📷 **3. Comandos para Imágenes**

| 🛠️ **Comando**                               | 📝 **Descripción**                                    |
|---------------------------------------------|-----------------------------------------------------|
| `docker images`                             | Listar imágenes locales.                            |
| `docker pull <imagen>`                      | Descargar imagen desde Docker Hub.                 |
| `docker build -t <imagen> <ruta>`    | Crear imagen desde un Dockerfile.                  |
| `docker push <imagen>`               | Subir imagen a Docker Hub.                         |
| `docker rmi <imagen>`                | Borrar una imagen.                                 |
| `docker history <imagen>`           | Ver historial de la imagen.                        |
| `docker inspect <imagen>`           | Ver detalles de una imagen.                        |

### 🌟 **Ejemplos Prácticos**  
- **Eliminar todas las imágenes locales**  
  ```bash
  docker rmi $(docker images -q)
  ```  

- **Crear una imagen desde un contenedor**  
  ```bash
  docker commit <contenedor> <imagen>
  ```

- **Exportar una imagen a un archivo**  
  ```bash
  docker save -o backup.tar <imagen>
  ```

- **Importar una imagen desde un archivo**  
  ```bash
  docker load -i backup.tar
  ```

---

## 🗄️ **4. Comandos para Volúmenes**  

| 🛠️ **Comando**                                         | 📝 **Descripción**                                  |
|--------------------------------------------------------|---------------------------------------------------|
| `docker volume create <volumen>`                | Crear un volumen nuevo.                          |
| `docker volume ls`                                     | Listar todos los volúmenes.                      |
| `docker volume inspect <volumen>`               | Ver detalles de un volumen.                      |
| `docker run -v <volumen>:<ruta>`                | Montar un volumen en un contenedor.              |
| `docker volume prune`                                  | Eliminar volúmenes no utilizados.                |
| `docker volume rm <volumen>`                    | Eliminar un volumen.                             |

### 🌟 **Ejemplos Prácticos**  
- **Crear un volumen nuevo**  
  ```bash
  docker volume create mi_volumen
  ```

- **Montar un volumen en un contenedor**  
  ```bash
  docker run -d -v mi_volumen:/data nginx
  ```

- **Eliminar todos los volúmenes no usados**  
  ```bash
  docker volume prune
  ```

---

## ⚙️ **5. Otras Opciones Útiles**  

| 🛠️ **Comando**                               | 📝 **Descripción**                                    |
|---------------------------------------------|-----------------------------------------------------|
| `docker-compose up -d`                      | Iniciar servicios con `docker-compose`.            |
| `docker network`                            | Administrar redes de contenedores.                  |
| `docker system prune`                       | Eliminar recursos no utilizados.                   |
| `docker-compose stop`                       | Detener los servicios activos.                     |
| `docker-compose down`                       | Detener y eliminar contenedores y redes.           |
| `docker-compose down -v`                    | Eliminar volúmenes junto con los servicios.        |

---

## 🎯 **Tips Adicionales**  

- **Mostrar el uso de disco**:  
  ```bash
  docker system df
  ```  

- **Limpiar todo Docker (con cuidado)**:  
  ```bash
  docker system prune -a
  ```  

- **Reiniciar el daemon de Docker**:  
  ```bash
  sudo systemctl restart docker
  ```  

- **Ver logs de un contenedor**:  
  ```bash
  docker logs <contenedor>
  ```

---
