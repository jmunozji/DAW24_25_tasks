---
title: 'CH3 - RSN'
---

# Servidores Web 🌐
Los servidores web, son utilizados para realizar una conexión mediante cliente servidor. Una vez realizada dicha conexión el cliente podrá acceder a los datos visibles                                                                                                                                                                                                                                                                                                                                                                                                                                         proporcionados por el servidor, pero para que éstos datos sean visibles y el usuario pueda acceder al propio servidor, se deben realizar una serie de comandos para poder entrar.

## Activación HTTPS en NGINX ▶️

| Comandos 🛡️ | Explicación 🗨️ |
-------------------- | -----------------------
| `sudo apt update && sudo apt upgrade` | Actualización de repositorios y sistema |
| `sudo apt install nginx` | Instalación de nginx |
| `systemctl status nginx` | Comprobación de Instalación |


## Dockerización NGINX 📦

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `nano Dockerfile` | Creación de Dockerfile, donde se habilitarán puertos, permisos... |
| `docker build -t servernginx` | Creación de la imagen nginx |
| `docker run --name=miservernginx -d -p 80:80 servernginx` | Creación del contenedor nginx |
| `docker stop miservernginx` | Parar contenedor |
| `docker rm miservernginx` | Borrar contenedor |
| `mkdir -p ~/web` | Creación de directorio en máquina host |
| `bash docker run -d --name=miservernginx \ --mount type=bind,source=/home/admin/web,target=/var/www/html \ -p 80:80 \ servernginx` | Lanzar nuevamente docker run, con nuevos parámetros |
| `docker run -d --name=miservernginx2 \-v /home/admin/web:/usr/share/nginx/html:ro \-p 80:80 \nginx:latest` | Lanzar nuevamente, pero desde la imagen oficial |


## Creación de un sitio virtual 🖥️

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo chown -R www-data:www-data /var/www/html` | Hacer que el directorio pertenezca al usuario www-data
| `sudo chmod 644 /var/www/html/index.html` | Permisos de lecturo y escritura, y el grupo solo de lectura
| `sudo chmod 755 /var/www/html/images` | Para que el servidor web pueda leer y navegar entre directorios  | 
| `sudo useradd -m usuario` | Creación de usario |
| `sudo usermod -g www-data usuario1` | Cambio de grupo del usuario |
| `sudo passwd usuario1` | Creación de contraseña "usuario1" |
| `sudo mkdir -p /var/www/sitio1` | Creación de espacio para el sitio virtual |
| `sudo chown usuario1:www-data /var/www/sitio1`| Asignación de propietario y grupo correcto |
| `sudo chmod 755 /var/www/sitio1` | Asignación de permisos adecuados | 
| `su usuario1` | Entrando al terminal como usuario1 |
| `nano /var/www/sitio1/index.html` | Creación del html para el sitio virtual |
| `sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/` | Creación de archivo simbólico | 
| `sudo systemctl restart nginx` | Reinicio de servidor para aplicar cambios | 
| `google-chrome --host-resolver-rules="MAP midominio.com 123.45.67.89, MAP otrodominio.com 98.76.54.32" --ignore-certificate-errors` | Si no tuvieramos permisos en la máquina host, habría que aplicar este comando |
| `google-chrome --host-resolver-rules="MAP sitio1 IPSERVER" --ignore-certificate-error` | Hay que sustituir IP server por la de AWS |


## Autenticación ✅

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `mkdir intranet` | Creación de carpeta intranet |
| `sudo apt install apache2-utils` | Instalación de utilidades para apache2 |
| `sudo htpasswd -c /etc/nginx/.htpasswd profe` | Creación de archivo oculto en el directorio de configuración |
| `sudo htpasswd /etc/nginx/.htpasswd otro_usuario` | Mismo paso para usuarios sin la -c |
| `tail -n 3 /var/log/nginx/access.log` | Obtención IP peticiones  |
| `sudo htpasswd -c /etc/apache2/.htpasswd profe` | Creación de archivo oculto apache2 |


## Activación HTTPS ♻️

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `mkdir /etc/nginx/ssl` | Creación directorio para generar certificados |
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt` | Comando para generar certificados |
| `mkdir /etc/apache2/ssl` | Creación directorio para generar certificados en apache2 |
| `ls -la /etc/apache2/mods-enabled/ssl*` | Comprobación si el modulo necesario esta activo |
| `sudo a2enmod ssl` | Activación de modulo necesario |
| `sudo systemctl restart apache2` | Reiniciar el servicio para aplicar cambios |


## Instalación de pila LAMP 🔋

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo apt update && sudo apt upgrade` | Actualización de repositorios y sistema |
| `sudo apt install apache2` | Instalación servicio apache2 |
| `sudo systemctl status apache2` | Comprobación de la instalación |
| `sudo apt install mariadb-server` | Instalación del servicor de mariadb |
| `sudo systemctl status mysql` | Comprobación de que el servicio está en marcha |
| `sudo mysql_secure_installation` | Ejecución de script que ayuda a securizar el servicio |
| `sudo apt-get install php libapache2-mod-php php-mysql` | Instalación de PHP |
| `sudo systemctl restart apache2` | Reinicio del servicio de apache2 |
| `sudo apt-get install phpmyadmin` | Instalación de phpMyAdmin |
| `sudo ln -s /usr/share/phpmyadmin/ /var/www/html/phpmyadmin` | Enlace símbolico por si no se pudiera acceder a phpMyAdmin |


## Instalación de pila LAMP dockerizada 🔋📦

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- 
| `nano docker-compose.yml` | Edición del archivo de docker  |
| `docker-compose up -d` | Iniciación del proceso |
| `docker ps` | Comprobación de que el contenedor funciona correctamente |
| `mkdir -p /home/usuario/practica6-2/www/html` | Creación carpeta para levantar el contenedor |
| `nano /home/usuario/practica6-2/www/html/index.php` | Creación documento para levantar el contenedor |
| `mkdir /home/usuario/practica6-2/nginx` | Creación de directorio nginx |
| `nano /home/usuario/practica6-2/nginx/default.conf` | Creación de archivo de configuración |
| `nano /home/usuario/practica6-2/nginx/Dockerfile` | Creación Dockerfile dentro del directior de nginx |
| `cd /home/usuario/practica6-2` | Cambio de directorio |
| `docker-compose up -d` | Levantamiento del contenedor |
| `docker ps` | Comprobación de si los contenedores están funcionando |


## Configuraciones de archivos 📜


```
> Configuración dockerfile nginx (P1)

FROM debian:latest
MAINTAINER Raul Simarro <alusim0931@ieselcaminas.org>

# Actualizamos e instalamos Nginx
RUN apt update && apt upgrade -y && apt install -y nginx && \
    rm -rf /var/lib/apt/lists/*

# Exponemos el puerto 80
EXPOSE 80

# Comando para iniciar Nginx en primer plano
CMD ["nginx", "-g", "daemon off;"]
```

```html
> Configuración html (P1)
<!DOCTYPE html>
<html>
<head>
<title>Servidor dockerizado!</title>
<style>
html { color-scheme: light dark; }
body { 
    width: 35em; 
    margin: 0 auto; 
    font-family: Tahoma, Verdana, Arial, sans-serif; 
    background-color: #f0f8ff;
    color: #333;
}
h1 {
    color: #ff4500;
}
p {
    color: #4682b4;
}
</style>
</head>
<body>
<h1>Bienvenido al servidor web dockerizado!</h1>
<p>Este es un servidor web dockerizado.</p>
</body>
</html>
```

```html
> Creación html (P2)

<!DOCTYPE html>
<html>
<head>
<title>Sitio1!</title>
</head>
<body>
<h1>Bienvendido al sitio1!</h1>
<p> Este es el sitio1 de usuario1</p>
</body>
</html>
```

```nginx
> Archivo configuración nginx (P2)

server {
        listen 80;
        listen [::]:80;
        root /var/www/sitio1;
        index index.html index.htm index.nginx-debian.html;
        server_name sitio1;
        location / {
                try_files $uri $uri/ =404;
        }
}
```    

```dockerfile
> Configuración docker-compose (P2)

services:
    nginx:
        build: .
        volumes:
            - ../sitiosvirtuales:/var/www
            - ./sites-available:/etc/nginx/sites-available
        ports:
            - 80:80
```


```dockerfile
> Configuración dockerfile (P2)
FROM debian:latest
MAINTAINER Raul Simarro <alusim0931@ieselcaminas.org>

# Actualizamos e instalamos Nginx
RUN apt update && apt upgrade -y && apt install -y nginx && \
    rm -rf /var/lib/apt/lists/*

# Copiar el script de arranque
COPY entrypoint.sh /entrypoint.sh

# Hacer que sea ejecutable
RUN chmod +x /entrypoint.sh

# Exponemos el puerto 80
EXPOSE 80

# Establecer el script como el entrypoint
ENTRYPOINT ["/entrypoint.sh"]
```

```nginx
>Configuración ficher nginx (P3)

server {
        listen 80;
        listen [::]:80;
        root /var/www/sitio1;
        index index.html index.htm index.nginx-debian.html;
        server_name sitio1;
        location / {
            auth_basic  "Área restringida";
            auth_basic_user_file    /etc/nginx/.htpasswd;
            try_files $uri $uri/ =404;
        }
}
```

```apache
>Configuración sitio1 (P3)

<VirtualHost *:80>
        ServerName sitio1
        DocumentRoot /var/www/sitio1
        <Directory /var/www/sitio1>
                AllowOverride All
                Require all granted
        </Directory>
        <Directory /var/www/sitio1/intranet>
                AuthType Basic
                AuthName "Intranet"
                AuthBasicProvider file
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
</VirtualHost>
```

```apache
> Configuración fichero sitio1 para https

<VirtualHost *:443>
    ServerName sitio1
    DocumentRoot /var/www/sitio1

    #Activa SSL
    SSLEngine on
    # Especifica la ruta a tu certificado SSL y clave privada
    SSLCertificateFile /etc/apache2/ssl/server.crt
    SSLCertificateKeyFile /etc/apache2/ssl/server.key

    <Directory /var/www/sitio1>
            AllowOverride All
            Require all granted
    </Directory>
</VirtualHost>
```

```nginx
>Creación docker-compose.yml (P6)
nginx:
  image: nginx:latest
  container_name: nginx-container
  ports:
    - 80:80
```

```html
> Creación del fichero html (P6)
<!DOCTYPE html>
<head>
  <title>¡Hola mundo!</title>
</head>

<body>
  <h1>¡Hola mundo!</h1>
  <p><?php echo 'Estamos corriendo PHP, version: ' . phpversion(); ?></p>
</body>
```

```nginx
> Configuración para que pueda correr la aplicación PHP
server {

     listen 80 default_server;
     root /var/www/html;
     index index.html index.php;

     charset utf-8;

     location / {
      try_files $uri $uri/ /index.php?$query_string;
     }

     location = /favicon.ico { access_log off; log_not_found off; }
     location = /robots.txt { access_log off; log_not_found off; }

     access_log off;
     error_log /var/log/nginx/error.log error;

     sendfile off;

     client_max_body_size 100m;

     location ~ .php$ {
      fastcgi_split_path_info ^(.+.php)(/.+)$;
      fastcgi_pass php:9000;
      fastcgi_index index.php;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_intercept_errors off;
      fastcgi_buffer_size 16k;
      fastcgi_buffers 4 16k;
    }

     location ~ /.ht {
      deny all;
     }
    }
```

```docker
> Configuración docker-compose

services:
  nginx:
    build: ./nginx/
    container_name: nginx-container
    ports:
      - 80:80
    links:
      - php
    volumes_from:
      - app-data
  php:
    build: ./php/
    container_name: php-container
    expose:
      - 9000
    links:
      - mysql
    volumes_from:
      - app-data

  app-data:
    image: php:7.0-fpm
    container_name: app-data-container
    volumes:
      - ./www/html/:/var/www/html/
    command: "true"

  mysql:
    image: mysql:5.7
    container_name: mysql-container
    volumes_from:
      - mysql-data
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: mydb
      MYSQL_USER: myuser
      MYSQL_PASSWORD: password

  mysql-data:
    image: mysql:5.7
    container_name: mysql-data-container
    volumes:
      - /var/lib/mysql
    command: "true"
```

> Todas las conexiónes se han realizado desde el servidor de AWS academy
