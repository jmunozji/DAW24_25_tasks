## Instalación
- `sudo apt-get update && sudo apt-get upgrade`
- [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) 
- [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
- Comprobar que se ha instalado correctamente
: `sudo docker version`

## Post instalación

- Permitir administrar Docker a un usuario sin privilegios
: `sudo usermod -aG docker $USER`
- Instalar Docker Compose
: `sudo apt install docker-compose`
- Iniciar un contenedor de ejemplo
: `sudo docker run hello-world`

## Contenedores

- Crear un contenedor y lanzar una terminal
: `docker run -it --name=ubuntu_pruebas ubuntu /bin/bash`
- Listar contenedores en ejecución
: `docker ps`
- Listar los contenedores del sistema, tanto parados como en ejecución
: `docker ps -a`
- Arrancar un contenedor ya creado
: `docker start <identificador/nombre>`
- Parar un contenedor ya creado
: `docker stop <identificador/nombre>`
- Parar todos los contenedores
: `docker stop $(docker ps -a -q)`
- Mostrar detalles de la configuración de un contenedor
: `docker inspect <identificador/nombre>`
- Ejecutar un comando dentro de un contenedor en ejecución
: `docker exec [opciones] <identificador/nombre> comando [argumentos]`
- Copiar ficheros y directorios del anfitrión a un contenedor o viceversa
: `docker cp <ruta_local> <nombre_contenedor>:<ruta_destino>`
- Conectar un terminal a la entrada y salida de un contenedor en ejecución
: `docker attach <nombre>`
- Ver los registros de salida de un contenedor en ejecución
: `docker logs`
- Borrar un contenedor parado
: `docker rm <identificador/nombre>`
- Borrar todos los contenedores parados
: `docker rm $(docker ps -a -q)`

## Imágenes

- Obtener información de las imágenes almacenadas localmente
: `docker images`
- Obtener información de imágenes que podemos descargar en el registro
: `docker search`
- Almacenar imágenes localmente desde el registro sin necesidad de crear un contenedor
: `docker pull <imagen:tag>`
- Observar el historial de una imagen creada
: `docker history <imagen>`
- Eliminar imágenes almacenadas localmente
: `docker rmi <imagen:tag>`
- Eliminar todas las imágenes locales que no estan siendo usadas por un contenedor
: `docker rmi $(docker images -q)`
- Eliminar todas las imágenes y contenedores parados
: `docker system prune -a`
- Crear una nueva imagen a partir de un contenedor existente
: `docker commit -a <autor> -m <comentario> <id/nombre-contenedor>
usuario/imagen:<version>`
- Añadir una nueva etiqueta a una imagen
: `docker tag usuario/imagen:tag usuario/imagen:tag-nuevo`
- Borrar una etiqueta
: `docker rmi usuario/imagen:tag`. La imagen se mantendrá mientras tenga otra etiqueta.
- Hacer una copia de una imagen de nuestro servidor local
: `docker save > usuario/imagen archivo-de-copia`
- Importar el fichero para crear una imagen en nuestra máquina
: `docker load < archivo`
- Dockerfile
: *`from`*`imagen` *`run`*`apt update && apt install -y nano` *`cmd`*`/bin/bash`
- Ejecutar lo que marca el Dockerfile
: `docker build -t ubuntunano ./`
- Indicar los puertos por defecto expuestos que tendrá un
contenedor basado en esta imagen
: `EXPOSE <puertos>`

### Subir una imagen a un repositorio (Docker Hub)

- Loguearse mediante consola al repostitorio
: `docker login`
- Hacer un commit local de la imagen
: `docker commit -a <autor> -m <comentario> <id/nombre-contenedor>
usuario/imagen:<version>`
- Subirlo usando "docker push"
: `docker push usuario/imagen`

## Volúmenes

- Crear volumen
: `docker volume create <nombre>`
- Visualizar los volúmenes disponibles
: `docker volume ls`
- Ver los metadatos de un volumen
: `docker volume inspect <nombre>`
- Borrar un volumen
: `docker volume rm <nombre>`

## Integrar contenedores y volúmenes

- Crear una base de datos usando un volumen para guardar los datos
: `docker run -d --name <nombre> \
    --mount source=<volumen>,target=/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=<nombre-database> \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    <imagen>`
- Otro ejemplo
: `docker run -d --name wordpress \
    --link wordpress-db:mysql \
    --mount type=bind,source="$(pwd)"/target,target=/var/www/html \
    -e WORDPRESS_DB_USER=manager \
    -e WORDPRESS_DB_PASSWORD=secret \
    -p 8080:80 \
    wordpress:4.9.8`
    - --link wordpress-db:mysql: indica que este contenedor se vincula con otro llamado "wordpress-db", y permite que este contenedor lo referencie utilizando el alias "mysql"
    - -d para lanzar en 2º plano
    - --mount para enlazar un directorio en el contenedor con un directorio en la máquina anfitrión
    - -e para definir parámetros en el contenedor
    - -p 8080:80 para enlazar el puerto 8080 en el anfitrión con el 80 en el contenedor.

## Docker Compose

- Crear fichero de configuración `docker-compose.yaml`
- Iniciar servicios
: `docker-compose up -d`
- Ver contenedores
: `docker-compose ps`
- Detener servicios
: `docker-compose stop`
- Borrar servicios
: `docker-compose down`
- Borrar también los volúmenes
: `docker-compose down -v`

## Trucos

- Borrar objetos que no están en uso
: `docker system prune`
- Borrar volúmenes que no están asociados a ningún contenedor
: `docker volume rm $(docker volume ls -q -f "dangling=true")`
- Borrar contenedores que han terminado su ejecución
: `docker rm $(docker ps -q -f "status=exited")`
- Borrar imágenes que no están etiquetadas
: `docker rmi $(docker images -q -f "dangling=true")`
- Hacer una copia de seguridad
: `docker run --rm -v /tmp:/backup \
    --volumes-from <container-name> \
    busybox tar -cvf /backup/backup.tar <path-to-data>`
- Restaurar
: `docker run --rm -v /tmp:/backup \
    --volumes-from <container-name> 
    busybox tar -xvf /backup/backup.tar <path-to-data>`
