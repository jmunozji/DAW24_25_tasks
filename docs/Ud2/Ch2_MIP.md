# Docker Cheatsheet

## Instalación y Configuración Básica
```bash
sudo apt-get update && sudo apt-get upgrade
sudo docker version
sudo usermod -aG docker $USER
sudo apt install docker-compose
```

## Comandos Básicos de Docker
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker run -it --name=ubuntu_pruebas ubuntu /bin/bash` | Crea y accede a un contenedor interactivo. |
| `docker exec -d ubuntu_pruebas touch /tmp/prueba` | Ejecuta un comando en un contenedor existente. |
| `docker exec -it ubuntu_pruebas bash`        | Accede a un contenedor en ejecución.           |
| `docker exec -it -e VAR=1 ubuntu_pruebas bash` | Ejecuta en contenedor con variable de entorno. |

## Logs y Visualización
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker run -d --name=muchotexto busybox sh -c "while true; do $(echo date) ; sleep 1; done"` | Ejecuta comando en bucle dentro del contenedor. |
| `docker attach muchotexto`                   | Adjunta a un contenedor en ejecución.          |
| `docker logs -f --until=2s muchotexto`       | Visualiza logs recientes del contenedor.       |

## Manejo de Volúmenes
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker volume create wordpress-db`          | Crea un volumen Docker.                        |
| `docker volume ls`                           | Lista volúmenes existentes.                    |
| `docker volume inspect wordpress-db`         | Inspecciona un volumen específico.             |
| `docker volume rm wordpress-db`              | Elimina un volumen.                            |
| `docker run -d -it --name appcontainer -v /home/sergi/target:/app nginx:latest` | Crea contenedor con volumen “binding mount”. |
| `docker run -d -it --name appcontainer -v micontenedor:/app nginx:latest` | Crea contenedor con volumen Docker. |
| `docker run -d -it --tmpfs /app nginx`       | Crea un contenedor con volumen tmpfs.          |
| `docker run --rm --volumes-from contenedor1 -v /home/sergi/backup:/backup ubuntu bash -c "cd /datos && tar cvf /backup/copiaseguridad.tar ."` | Backup de volúmenes. |

## Docker Compose para WordPress
1. Crear el directorio de trabajo:
   ```bash
   mkdir -p ~/Sites/wordpress/target && cd ~/Sites/wordpress
   ```

2. Crear `docker-compose.yaml` en `~/Sites/wordpress`:
   ```yaml
   version: '3'
   services:
     db:
       image: mariadb:10.3.9
       volumes:
         - data:/var/lib/mysql
       environment:
         - MYSQL_ROOT_PASSWORD=secret
         - MYSQL_DATABASE=wordpress
         - MYSQL_USER=manager
         - MYSQL_PASSWORD=secret
     web:
       image: wordpress:4.9.8
       depends_on:
         - db
       volumes:
         - ./target:/var/www/html
       environment:
         - WORDPRESS_DB_USER=manager
         - WORDPRESS_DB_PASSWORD=secret
         - WORDPRESS_DB_HOST=db
       ports:
         - 8080:80
   volumes:
     data:
   ```

3. Comandos básicos de Docker Compose:
   ```bash
   docker-compose up -d
   docker-compose ps
   docker-compose stop
   docker-compose down
   docker-compose down -v
   ```

## Docker Compose para Portainer
1. Crear el directorio de trabajo:
   ```bash
   mkdir -p ~/Sites/portainer && cd ~/Sites/portainer
   ```

2. Crear `docker-compose.yaml` en `~/Sites/portainer`:
   ```yaml
   version: '2'
   services:
     portainer:
       image: portainer/portainer
       command: -H unix:///var/run/docker.sock
       volumes:
         - /var/run/docker.sock:/var/run/docker.sock
         - portainer_data:/data
       ports:
         - 9000:9000
   volumes:
     portainer_data:
   ```

3. Iniciar Portainer:
   ```bash
   docker-compose up -d
   ```

## Limpieza del Sistema
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker system prune`                        | Elimina contenedores, volúmenes y redes no utilizados. |
| `docker volume rm $(docker volume ls -q -f "dangling=true")` | Elimina volúmenes no utilizados. |
| `docker rm $(docker ps -q -f "status=exited")` | Elimina contenedores detenidos. |
| `docker rmi $(docker images -q -f "dangling=true")` | Elimina imágenes no etiquetadas. |

## Backup y Restauración de Volúmenes
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker run --rm -v /tmp:/backup --volumes-from <container-name> busybox tar -cvf /backup/backup.tar <path-to-data>` | Backup de datos en contenedor. |
| `docker run --rm -v /tmp:/backup --volumes-from <container-name> busybox tar -xvf /backup/backup.tar <path-to-data>` | Restaurar datos en contenedor. |

## Docker Compose para WordPress
1. Crear el directorio de trabajo:
   ```bash
   mkdir -p ~/Sites/wordpress/target && cd ~/Sites/wordpress
   ```

2. Crear `docker-compose.yaml` en `~/Sites/wordpress`:
   ```yaml
   version: '3'
   services:
     db:
       image: mariadb:10.3.9
       volumes:
         - data:/var/lib/mysql
       environment:
         - MYSQL_ROOT_PASSWORD=secret
         - MYSQL_DATABASE=wordpress
         - MYSQL_USER=manager
         - MYSQL_PASSWORD=secret
     web:
       image: wordpress:4.9.8
       depends_on:
         - db
       volumes:
         - ./target:/var/www/html
       environment:
         - WORDPRESS_DB_USER=manager
         - WORDPRESS_DB_PASSWORD=secret
         - WORDPRESS_DB_HOST=db
       ports:
         - 8080:80
   volumes:
     data:
   ```

3. Comandos básicos de Docker Compose:
   ```bash
   docker-compose up -d
   docker-compose ps
   docker-compose stop
   docker-compose down
   docker-compose down -v
   ```

## Docker Compose para Portainer
1. Crear el directorio de trabajo:
   ```bash
   mkdir -p ~/Sites/portainer && cd ~/Sites/portainer
   ```

2. Crear `docker-compose.yaml` en `~/Sites/portainer`:
   ```yaml
   version: '2'
   services:
     portainer:
       image: portainer/portainer
       command: -H unix:///var/run/docker.sock
       volumes:
         - /var/run/docker.sock:/var/run/docker.sock
         - portainer_data:/data
       ports:
         - 9000:9000
   volumes:
     portainer_data:
   ```

3. Iniciar Portainer:
   ```bash
   docker-compose up -d
   ```

## Limpieza del Sistema
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker system prune`                        | Elimina contenedores, volúmenes y redes no utilizados. |
| `docker volume rm $(docker volume ls -q -f "dangling=true")` | Elimina volúmenes no utilizados. |
| `docker rm $(docker ps -q -f "status=exited")` | Elimina contenedores detenidos. |
| `docker rmi $(docker images -q -f "dangling=true")` | Elimina imágenes no etiquetadas. |

## Backup y Restauración de Volúmenes
| Comando                                      | Descripción                                     |
|----------------------------------------------|-------------------------------------------------|
| `docker run --rm -v /tmp:/backup --volumes-from <container-name> busybox tar -cvf /backup/backup.tar <path-to-data>` | Backup de datos en contenedor. |
| `docker run --rm -v /tmp:/backup --volumes-from <container-name> busybox tar -xvf /backup/backup.tar <path-to-data>` | Restaurar datos en contenedor. |

