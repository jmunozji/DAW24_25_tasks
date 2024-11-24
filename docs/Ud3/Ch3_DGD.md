---
title: 'CH3-DGD'
---

# CONFIGURACION SERVIDORES WEB

## NGINX

### 📝 Instalación de Nginx

```bash
sudo apt update && sudo apt upgrade
sudo apt-get install nginx
```

### 📁 Directorios de configuración

Directorio | Descripción
----------- | -----------
/etc/nginx | Configuración de Nginx
/etc/nginx/sites-available | Configuración de sitios web
/etc/nginx/sites-enabled | Sitios web habilitados
/var/www/html | Contenido de los sitios web
/var/log/nginx | Registros de Nginx



#### 💪 Crear el directorio de la web y el index.html

```bash
sudo mkdir /var/www/sitio1
sudo touch /var/www/sitio1/index.html
```

#### ⚙️ Configuración de permisos

```bash
sudo chown -R www-data:www-data /var/www/sitio1
```

#### 💪 Crear el archivo de configuración

```bash
cd /etc/nginx/sites-available
```

```bash
sudo nano sitio1
```

#### Configuración del sitio web

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

#### Habilitar el sitio web

```bash
cd /etc/nginx/sites-enabled
sudo ln -s /etc/nginx/sites-available/sitio1 sitio1
```

#### Reiniciar Nginx

```bash
sudo systemctl restart nginx
```


### ✅ Autenticación de usuarios con Nginx

```bash
sudo apt install apache2-utils
```

```bash
htpasswd -c /etc/nginx/.htpasswd usuario
```

#### 💡 Configuración de autenticación en Nginx

```bash
sudo mkdir /var/www/sitio1/secret
```

```diff
server {
    listen 80;
    listen [::]:80;
    root /var/www/sitio1;
    index index.html index.htm index.nginx-debian.html;
    server_name sitio1;
    location / {
        try_files $uri $uri/ =404;
    }
+   location /secret {
+       auth_basic "Restricted";
+       auth_basic_user_file /etc/nginx/.htpasswd;
+       root /var/www/sitio1;
+   }
}
```


### ✅ Autenticación por IP con Nginx

```diff
server {
    listen 80;
    listen [::]:80;
    root /var/www/sitio1;
    index index.html index.htm index.nginx-debian.html;
    server_name sitio1;
    location / {
        try_files $uri $uri/ =404;
    }
    location /secret {
+       satisfy any;
+       allow 192.168.1.0/24;
+       deny all;
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd;
        root /var/www/sitio1;
    }
}
```

### Activación de https

#### 💡 Creacion del certificado

```bash
sudo mkdir /etc/nginx/ssl
```

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
```

#### Configuracion del sitio para https

```diff
server {
-    listen 80;
-    listen [::]:80;
+    listen 443 ssl;
+    ssl_certificate /etc/nginx/ssl/nginx.crt;
+    ssl_certificate_key /etc/nginx/ssl/nginx.key;
+    ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
+    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    root /var/www/sitio1;
    index index.html index.htm index.nginx-debian.html;
    server_name sitio1;
    location / {
        try_files $uri $uri/ =404;
    }
    location /secret {
        satisfy any;
        allow 192.168.1.0/24;
        deny all;
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd;
        root /var/www/sitio1;
    }
}
```

#### Redireccion de https

    server {
        listen 80;
        listen [::]:80;
        server_name sitio1;
        return 301 https://$host$request_uri;
    }


## APACHE2

### 📝 Instalación de Apache2

```bash
sudo apt update && sudo apt upgrade
sudo apt install apache2
```

### 📁 Directorios de configuración

Directorio | Descripción
----------- | -----------
/etc/apache2 | Configuración de Apache2
/etc/apache2/sites-available | Configuración de sitios web
/etc/apache2/sites-enabled | Sitios web habilitados
/var/www/html | Contenido de los sitios web
/var/log/apache2 | Registros de Apache2

#### 💪 Crear el directorio de la web y el index.html

```bash
sudo mkdir /var/www/sitio1
sudo touch /var/www/sitio1/index.html
```

#### ⚙️ Configuración de permisos

```bash
sudo chown -R www-data:www-data /var/www/sitio1
```

#### 💪 Crear el archivo de configuración

```bash
cd /etc/apache2/sites-available
```

```bash
sudo nano sitio1.conf
```

#### Configuración del sitio web

```xml
<VirtualHost *:80>
    DocumentRoot /var/www/sitio1
    ServerName sitio1
    <Directory /var/www/sitio1>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/sitio1_error.log
    CustomLog ${APACHE_LOG_DIR}/sitio1_access.log combined
</VirtualHost>
```

#### Habilitar el sitio web

```bash
sudo a2ensite sitio1.conf
sudo systemctl reload apache2
```

### ✅ Autenticación de usuarios con Apache2

```bash
sudo apt install apache2-utils
```

```bash
htpasswd -c /etc/apache2/.htpasswd usuario
```

#### 💡 Configuración de autenticación en Apache2

```bash
sudo mkdir /var/www/sitio1/secret
```

```diff
<Directory /var/www/sitio1/secret>
+   AuthType Basic
+   AuthName "Restricted Access"
+   AuthUserFile /etc/apache2/.htpasswd
+   Require valid-user
</Directory>
```

#### Reiniciar Apache2

```bash
sudo systemctl reload apache2
```

### ✅ Autenticación por IP con Apache2

```diff
<Directory /var/www/sitio1/secret>
    AuthType Basic
    AuthName "Restricted Access"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
+   Require ip 192.168.1.0/24
+   Require all denied
</Directory>
```

### Activación de HTTPS

#### 💡 Creación del certificado

```bash
sudo mkdir /etc/apache2/ssl
```

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

#### Configuración del sitio para HTTPS

```diff
<VirtualHost *:443>
+   SSLEngine on
+   SSLCertificateFile /etc/apache2/ssl/apache.crt
+   SSLCertificateKeyFile /etc/apache2/ssl/apache.key
    DocumentRoot /var/www/sitio1
    ServerName sitio1
    <Directory /var/www/sitio1>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/sitio1_ssl_error.log
    CustomLog ${APACHE_LOG_DIR}/sitio1_ssl_access.log combined
</VirtualHost>
```

#### Redirección de HTTP a HTTPS

```diff
<VirtualHost *:80>
+   Redirect "/" "https://sitio1/"
</VirtualHost>
``` 

#### Habilitar módulos necesarios y reiniciar Apache2

```bash
sudo a2enmod ssl
sudo a2enmod rewrite
sudo systemctl restart apache2
```


## Instalación Pila LAMP

```bash
sudo apt update && sudo apt upgrade

sudo apt install apache2 php libapache2-mod-php php-mysql

sudo apt install mariadb-server
```

### Configuración de MariaDB

```bash
sudo mysql_secure_installation
```

### Instalación de PHPMyAdmin

```bash
sudo apt install phpmyadmin
```
