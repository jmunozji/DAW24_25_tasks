---
title: "CH3 - BSO"
---

# Cheatshet 3 - Servidores Web y de Aplicaciones

## Índice

- [Apache2](#apache2)
  - [Instalación de Apache](#instalación-de-apache)
  - [Habilitar Apache como predeterminado al iniciar la máquina](#habilitar-apache-como-predeterminado-al-iniciar-la-máquina)
  - [Directorios esenciales de Apache](#directorios-esenciales-de-apache)
  - [Crear web de ejemplo y asignarle permisos](#crear-web-de-ejemplo-y-asignarle-permisos)
  - [Configuración del sitio web](#configuración-del-sitio-web)
  - [Autenticación](#autenticación)
  - [Habilitar HTTPS](#habilitar-https)
- [Nginx](#nginx)
  - [Instalación de Nginx](#instalación-de-nginx)
  - [Habilitar Nginx como predeterminado al iniciar la máquina](#habilitar-nginx-como-predeterminado-al-iniciar-la-máquina)
  - [Directorios esenciales de Nginx](#directorios-esenciales-de-nginx)
  - [Crear web de ejemplo y asignarle permisos](#crear-web-de-ejemplo-y-asignarle-permisos)
  - [Configuración del sitio web](#configuración-del-sitio-web)
  - [Autenticación básica](#autenticación-básica)
  - [Autenticación por IP](#autenticación-por-ip)
  - [Habilitar HTTPS](#habilitar-https)
- [Pila LAMP](#pila-lamp)
  - [Instalación de Pila LAMP](#instalación-de-pila-lamp)
  - [Instalación de Apache, PHP y MariaDB](#instalación-de-apache-php-y-mariadb)
  - [Configuración de la base de datos MariaDB](#configuración-de-la-base-de-datos-mariadb)
  - [Verificación de PHP](#verificación-de-php)
  - [Instalación de phpMyAdmin](#instalación-de-phpmyadmin)
  - [Pila LAMP en Docker Compose](#pila-lamp-en-docker-compose)
  - [Dockerfile para Nginx](#dockerfile-para-nginx)
  - [Archivo docker-compose.yml](#archivo-docker-composeryml)

## Apache2

### Instalación de Apache

#### En una máquina Ubuntu o Debian (como las que usamos en AWS)

```bash
sudo apt update #En AWS no necesitas sudo
sudo apt install apache2
```

### Habilitar Apache como predeterminado al iniciar la máquina

```bash
sudo systemctl start apache2 #Inicia el servicio
sudo systemctl status apache2 #Comprueba el servicio
sudo systemctl enable apache2 #Habilita el servicio al iniciar el PC
```

### Directorios esenciales de Apache

- `/etc/apache2` Directorio de configuración de Apache2
- `/var/log/apache2` Directorio donde se guardan los registros de Apache2
- `/var/www/html` Directorio donde se alojan los contenidos de nuestra web
- `/etc/apache2/sites-available` Configuración de sitios web
- `/etc/apache2/sites-enabled` Directorio donde se alojan las webs que tenemos habilitadas

### Crear web de ejemplo y asignarle permisos

```bash
sudo mkdir /var/www/ejemplo #Esta sería nuestra ruta principal de la web 'localhost/ejemplo'
sudo touch /var/www/ejemplo/index.html #HTML que contiene el indice de esta web

sudo chown -R $USER:www-data /var/www/ejemplo #Asignamos el usuario y el grupo www-data de forma recursiva a la ruta principal
sudo chmod 755 /var/www/ejemplo #Asignamos los permisos correctos en este caso 7(rwx) y 5(rx)
```

### Configuración del sitio web

```bash
sudo nano /etc/apache2/sites-available/ejemplo.conf #Creamos el archivo de configuración de la web ejemplo
```

Archivo de configuración

```apache2
<VirtualHost *:80>
    ServerName ejemplo.com
    ServerAlias www.ejemplo.com
    DocumentRoot /var/www/ejemplo
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Activar el sitio web

```bash
sudo a2ensite ejemplo.conf
sudo systemctl reload apache2
```

### Autentificación

```bash
sudo apt install apache2-utils #Instalación de programas necesarios
```

```bash
htpasswd -c /etc/apache2/.htpasswd user #Creación de usuario y contraseña
```

```bash
sudo mkdir /var/www/ejemplo/areaprivada #Creamos una carpeta donde alojaremos el sitio restringido
```

```apache
<Directory /var/www/ejemplo/areaprivada>
    AuthType Basic
    AuthName "Área Privada"
    AuthBasicProvider file
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

```bash
sudo systemctl reload apache2 #Reiniciamos apache
```

### Habilitar HTTPS

```bash
sudo apt install openssl #Instalamos openSSL
sudo a2enmod ssl #Habilitamos el SSL
```

```bash
#Crear certificado
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/ejemplo.key -out /etc/ssl/certs/ejemplo.crt
```

```apache
<VirtualHost *:443>
    ServerName ejemplo.com
    DocumentRoot /var/www/ejemplo
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ejemplo.crt
    SSLCertificateKeyFile /etc/ssl/private/ejemplo.key
</VirtualHost>
```

```bash
sudo systemctl reload apache2 #Volveremos a reiniciar apache
```

## Nginx

### Instalación de Nginx

#### En una máquina Ubuntu o Debian (como las que usamos en AWS)

```bash
sudo apt update #En AWS no necesitas sudo
sudo apt install nginx
```

### Habilitar Nginx como predeterminado al iniciar la máquina

```bash
sudo systemctl start nginx #Inicia el servicio
sudo systemctl status nginx #Comprueba el servicio
sudo systemctl enable nginx #Habilita el servicio al iniciar el PC
```

### Directorios esenciales de Nginx

- `/etc/nginx` Directorio de configuración de Nginx
- `/var/log/nginx` Directorio donde se guardan los registros de Nginx
- `/var/www/html` Directorio donde se alojan los contenidos de nuestra web
- `/etc/nginx/sites-available` Configuración de sitios web
- `/etc/nginx/sites-enabled` Directorio donde se alojan las webs que tenemos habilitadas

### Crear web de ejemplo y asignarle permisos

```bash
sudo mkdir /var/www/ejemplo #Esta sería nuestra ruta principal de la web 'localhost/ejemplo'
sudo touch /var/www/ejemplo/index.html #HTML que contiene el indice de esta web

sudo chown -R $USER:www-data /var/www/ejemplo #Asignamos el usuario y el grupo www-data de forma recursiva a la ruta principal
sudo chmod 755 /var/www/ejemplo #Asignamos los permisos correctos en este caso 7(rwx) y 5(rx)
```

### Configuración del sitio web

```bash
sudo nano /etc/nginx/sites-available/ejemplo.conf #Creamos el archivo de configuración de la web ejemplo
```

Archivo de configuración

```nginx
server {
        listen 80;
        listen [::]:80;
        root /var/www/ejemplo;
        index index.html index.htm index.nginx-debian.html;
        server_name ejemplo.com;
        location / {
            try_files $uri $uri/ =404;
        }
    }
```

Activar el sitio web

```bash
sudo ln -s /etc/nginx/sites-available/ejemplo /etc/nginx/sites-enabled/
sudo systemctl reload nginx
```

### Autentificación

```bash
sudo apt install apache2-utils #Instalación de programas necesarios
```

```bash
htpasswd -c /etc/nginx/.htpasswd user #Creación de usuario y contraseña
```

```bash
sudo mkdir /var/www/ejemplo/areaprivada #Creamos una carpeta donde alojaremos el sitio restringido
```

```nginx
server{
    #Configuración anterior
    location /areaprivada/ {
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd;
        root /var/www/ejemplo;
    }
}
```

#### Autentificación por IP

```nginx
server{
    #Configuración anterior
    location /areaprivada/ {

        satisfy all; #Todas las reglas deben cumplirse(any -> Cualquiera de ellas)
        deny  172.1.10.6; #Bloquea el acceso a esta IP
        allow 172.1.10.15; #Permite el acceso a estas 2 IPs
        allow 172.1.3.14;
        deny  all; #Bloquea las demás IP's que no se hayan permitido, si se pone más arriba, se ignorará lo de abajo.

        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd;
        root /var/www/ejemplo;
    }
}
```

El orden de `allow` y `deny` es muy importante en Nginx, ya que se evalúan en el mismo orden en que aparecen.

```bash
sudo systemctl reload nginx #Reiniciamos Nginx
```

### Habilitar HTTPS

```bash
sudo apt install openssl #Instalamos openSSL
```

```bash
#Crear certificado
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/ejemplo.key -out /etc/nginx/ssl/ejemplo.crt
```

```nginx
server {
    listen 443 ssl;
    server_name ejemplo.com;
    ssl_certificate /etc/nginx/ssl/ejemplo.crt;
    ssl_certificate_key /etc/nginx/ssl/ejemplo.key;
    root /var/www/ejemplo;
    index index.html;
}
```

```bash
sudo systemctl reload nginx #Volveremos a reiniciar nginx
```

## Pila LAMP

### Instalación

```bash
sudo apt update && sudo apt upgrade
sudo apt install apache2 php libapache2-mod-php php-mysql mariadb-server
sudo systemctl restart apache2 #Reiniciamos Apache para aplique los cambios
sudo systemctl status mysql #Comprobamos el status de mariadb
```

### Configuración de la base de datos MariaDB

```bash
sudo mysql_secure_installation #Configuraremos mariadb
```

### Configuración de PHP

Comprobaremos que php se ejecuta correctamente con este pequeño archivo

```bash
sudo nano /var/www/html/info.php
```

```php
<?php
phpinfo();
?>
```

Deberiamos ver la información de php al dirigirnos aquí

`http://IPservidorDebian/info.php`

### Instalación de phpMyAdmin

```bash
sudo apt-get install phpmyadmin #Nos ejecutará un instalador y elegimos apache2
```

Deberiamos poder entrar a nuestro phpMyAdmin desde aquí

`http://IPservidorDebian/phpmyadmin`

## Pila LAMP en docker-compose

Dockerfile nginx

```Dockerfile
FROM nginx:latest #Descarga la imágen de nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf #Copia los archivos de configuración
COPY ./index.php /var/www/html/index.php #Copia el archivo de conexión a la base de datos de PHP
```

docker-compose.yml

```docker-compose.yml
services:
    nginx:
        build: ./nginx/ #Construimos el Dockerfile
        container_name: nginx-container
        ports:
        - 80:80 #Especificamos puertos del host y del docker
        volumes:
        - app-data:/var/www/html/ #linkeamos el volumen app-data de abajo con /var/www/html -> Que contiene nuestra web
    php:
        build: ./php/
        container_name: php-container
        expose:
        - 9000
        volumes:
        - app-data:/var/www/html/
    mysql:
        image: mysql:5.7
        container_name: mysql-container
        volumes:
        - mysql-data:/var/lib/mysql
        environment:
        MYSQL_ROOT_PASSWORD: secret
        MYSQL_DATABASE: mydb
        MYSQL_USER: myuser
        MYSQL_PASSWORD: password
volumes:
    app-data:
    mysql-data:
```

- `docker-compose up -d` crea y ejecuta todos los contenedores de docker-compose en segundo plano
- `docker-compose down` cierra todos los contenedores de docker-compose
- `docker-compose ps` muestra todos los contenedores en ejecución de docker-compose
