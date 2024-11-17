---
title: 'CH3 - RML'
---

# Cheatsheet de Servidores Web

## NGINX

### Instalación y gestión del servidor - Nginx

#### Instalación - Nginx

```bash
    sudo apt update && sudo apt upgrade
    sudo apt install nginx
```

#### Gestión del servidor - Nginx

##### Ver estado - Nginx

```bash
sudo systemctl status nginx
```

##### Iniciar - Nginx

```bash
sudo systemctl start nginx
```

##### Detener - Nginx

```bash
sudo systemctl stop nginx
```

##### Habilitar inicio automático - Nginx

```bash
sudo systemctl enable nginx
```

##### Deshabilitar inicio automático - Nginx

```bash
sudo systemctl disable nginx
```

#### Configuración básica - Nginx

##### Editar la página principal - Nginx

```bash
sudo nano /var/www/html/index.nginx-debian.html
```

#### Logs del Servidor - Nginx

##### Ver solicitudes del servidor - Nginx

```bash
sudo cat /var/log/nginx/access.log
```

##### Ver errores del servidor - Nginx

```bash
sudo cat /var/log/nginx/error.log
```

### Sitios virtuales - Nginx

#### Crear directorio para el sitio - Nginx

```bash
sudo mkdir -p /var/www/sitio1
sudo chown www-data:www-data /var/www/sitio1
sudo chmod 755 /var/www/sitio1
```

#### Crear archivo de configuración - Nginx

```bash
sudo nano /etc/nginx/sites-available/sitio1
```

##### Archivo de configuración - Nginx

```bash
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

#### Crear enlace simbólico - Nginx

```bash
sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

#### Comprobar su funcionamiento

##### Linux

```bash
sudo nano /etc/hosts
```

Añadimos la siguiente línea

```bash
127.0.0.1 sitio1
```

IP de la máquina local o IP publica del servidor(AWS) y nombre del sitio virtual

##### Windows

Accedemos aquí: `C:\Windows\System32\drivers\etc\hosts` y añadimos la misma línea que en linux

##### Sin permisos para modificar hosts

Si no tienes permisos para modificar el archivo de `hosts` puedes usar este comando

```bash
google-chrome --host-resolver-rules="MAP sitio1 IPSERVER" --ignore-certificate-error
```

### Autenticación - Nginx

#### Instalar herramienta - Nginx

```bash
sudo apt install apache2-utils
```

#### Crear archivo de contraseñas - Nginx

```bash
sudo htpasswd -c /etc/nginx/.htpasswd usuario
```

#### Modificar configuración - Nginx

```bash
location /intranet {
    auth_basic "Área restringida";
    auth_basic_user_file /etc/nginx/.htpasswd;
    try_files $uri $uri/ =404;
}
```

#### Restricciones de IP - Nginx

* deny -> Denegar el acceso a una IP específica

* allow -> Permite el acceso a una IP específica

* deny all -> Denega el acceso a todas las IP 

* satisfy all -> Debe cumplir todas las directivas, tanto las de IP y las de autentificación

```bash
location /intranet {
    satisfy all
    deny 192.168.1.100;
    allow 127.0.0.1;
    deny all;
    auth_basic "Área restringida";
    auth_basic_user_file /etc/nginx/.htpasswd;
    try_files $uri $uri/ =404;
}
```

### HTTPS - Nginx

#### Crearción certificados SSL - Nginx

```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt
```

#### Modificación de la configuración - Nginx

```bash
listen 443 ssl;
ssl_certificate /etc/nginx/ssl/server.crt;
ssl_certificate_key /etc/nginx/ssl/server.key;
```

#### Redirección forzosa a HTTPS - Nginx

```bash
server {
    listen 80;
    return 301 https://$host$request_uri;
}
```

## APACHE

### Instalación y gestión del servidor - Apache

#### Instalación - Apache

```bash
    sudo apt update && sudo apt upgrade
    sudo apt install apache2
```

#### Gestión del servidor - Apache

##### Ver estado - Apache

```bash
sudo systemctl status apache2
```

##### Iniciar - Apache

```bash
sudo systemctl start apache2
```

##### Detener - Apache

```bash
sudo systemctl stop apache2
```

##### Habilitar inicio automático - Apache

```bash
sudo systemctl enable apache2
```

##### Deshabilitar inicio automático - Apache

```bash
sudo systemctl disable apache2
```

#### Configuración básica - Apache

##### Editar la página principal - Apache

```bash
sudo nano /var/www/html/index.html 
```

#### Logs del Servidor - Apache

##### Ver solicitudes del servidor - Apache

```bash
sudo cat /var/log/apache2/access.log
```

##### Ver errores del servidor - Apache

```bash
sudo cat /var/log/apache2/error.log
```

### Sitios virtuales - Apache

#### Crear directorio para el sitio - Apache

```bash
sudo mkdir -p /var/www/sitio1
sudo chown www-data:www-data /var/www/sitio1
sudo chmod 755 /var/www/sitio1
```

#### Crear archivo de configuración - Apache

```bash
sudo nano /etc/apache2/sites-available/sitio1.conf
```

##### Archivo de configuración - Apache

```bash
<VirtualHost *:80>
    ServerName sitio1
    DocumentRoot /var/www/sitio1
    <Directory /var/www/sitio1>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

#### Crear enlace simbólico - Apache

```bash
sudo a2ensite sitio1.conf
sudo systemctl restart apache2
```

### Autenticación - Apache

#### Instalar herramienta - Apache

```bash
sudo apt install apache2-utils
```

#### Crear archivo de contraseñas - Apache

```bash
sudo htpasswd -c /etc/apache2/.htpasswd usuario
```

#### Modificar configuración - Apache

```bash
<Directory /var/www/sitio1/intranet>
    AuthType Basic
    AuthName "Intranet"
    AuthBasicProvider file
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

### HTTPS - Apache

#### Crearción certificados SSL - Apache

```bash
sudo mkdir -p /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/server.key -out /etc/apache2/ssl/server.crt
```

#### Activar módulo SSL - Apache

```bash
sudo a2enmod ssl
sudo systemctl restart apache2
```

#### Modificación de la configuración - Apache

```bash
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/server.crt
    SSLCertificateKeyFile /etc/apache2/ssl/server.key
    ServerName sitio1
    DocumentRoot /var/www/sitio1
</VirtualHost>
```

#### Redirección forzosa a HTTPS - Apache

```bash
<VirtualHost *:80>
    ServerName sitio1
    Redirect permanent / https://sitio1/
</VirtualHost>
```

## Pila LAMP

### MariaDB

#### Instalación - MariaDB

```bash
sudo apt install mariadb-server
```

#### Comprobar si el servicio está activo - MariaDB

```bash
sudo systemctl status mysql
```

#### Securización del servicio - MariaDB

```bash
sudo mysql_secure_installation
```

### PHP

#### Instalación - PHP

```bash
sudo apt-get install php libapache2-mod-php php-mysql
```

#### Reiniciar el servidor - PHP

```bash
sudo systemctl restart apache2
```

### PhpMyAdmin

#### Instalación - PhpMyAdmin

```bash
sudo apt-get install phpmyadmin
```
