---
title: 'Cheatsheet 2'
---

# **Cheatsheet Contenedores Docker** 🐋

## 1. **Instalación**

### **Instalación en Linux**

1. ##### Incluir el repositorio

Actualizar los paquetes con el comando:

```bash
sudo apt update
```

Instalar las dependencias:

```bash
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

Descargar la clave GPG e incluirla:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Añadir el repositorio:

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

2. #### Instalar Docker engine CE

Actualizar los paquetes del sistema otra vez e instalar Docker engine:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Instalar la herramienta Docker Compose:

```bash
sudo apt install docker-compose
```

3. #### Permitir a los usuarios utilizar Docker sin privilegios

Añadir al usuario deseado al grupo llamado docker 

```bash
sudo usermod -aG docker $USER
```

4. #### Desinstalación

Desinstalar Docker utilizaremos:

```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io
```

## **Contenedores**

Ejecutar un nuevo contenedor:

```bash
docker run <nombre_imagen>
```

Ejecutar un nuevo contenedor en segundo plano:

```bash
docker run -d <nombre_imagen>
```

Listar contenedores en ejecución:

```bash
docker ps
```

Listar todos los contenedores (incluidos los detenidos):

```bash
docker ps -a
```

Detener un contenedor:

```bash
docker stop <nombre_contenedor>
```

Eliminar un contenedor:

```bash

```

Ejecutar un comando en un contenedor en ejecución:

```bash
docker exec -it <nombre_contenedor> <comando>
```

Reiniciar un contenedor:

```bash
docker restart <nombre_contenedor>
```

## **Imágenes**

Listar imágenes:

```bash
docker images
```

Descargar una image:

```bash
docker pull <nombre_imagen>
```

Eliminar une imagen:

```bash
docker rmi <nombre_imagen>
```

Crear una imagen a partir de un Dockerfile:

```bash
docker build -t <nombre_imagen>:<tag> <directorio_dockerfile>
```

## Docker Compose

Iniciar servicios de docker-compose.yml:

```bash
docker-compose up
```

Detener servicios:

```bash
docker-compose down
```

Ver log de los servicios:

```bash
docker-compose logs
```

Construir imágenes antes de iniciar los servicios:

```bash
docker-compose up --build
```



```bash

```



```bash

```



```bash

```



```bash

```



```bash

```



```bash

```
