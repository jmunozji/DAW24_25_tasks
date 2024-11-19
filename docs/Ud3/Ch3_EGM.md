---
title: 'CH3 - EGM'
---

# Cheatsheet UD3: Configuración de Servidores Web

---
## ![NGINX Logo](https://upload.wikimedia.org/wikipedia/commons/c/c5/Nginx_logo.svg)

### 🚀 Instalación y Configuración Básica 🚀

📦 **Instalación en Ubuntu o Debian :** 

```bash
   > sudo apt update && sudo apt install nginx
```

🔌 **Habilitar y verificar servicio :**

```bash
   > sudo systemctl enable nginx
   > sudo systemctl start nginx
   > sudo systemctl status nginx
```

### 📁 Directorios esenciales de Nginx 📁

| **📂 Directorio**                     | **📝 Descripción**                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------------|
| **`/etc/nginx`**                          | Directorio de configuración de NGINX.                                            |
| **`/var/log/nginx`**                      | Directorio donde se guardan los registros de NGINX.                              |
| **`/var/www/html`**                       | Directorio donde se alojan los contenidos de nuestra web.                        |
| **`/etc/nginx/sites-available`**          | Configuración de sitios web disponibles.                                         |
| **`/etc/nginx/sites-enabled`**            | Directorio donde se alojan las webs que tenemos habilitadas.                     |

### Gestión de Permisos

```bash
    > sudo mkdir /var/www/mi_sitio
    > sudo touch /var/www/mi_sitio/index.html 
    > sudo chown -R $USER:www-data /var/www/mi_sitio
    > sudo chmod 755 /var/www/mi_sitio
```

### Configurar un sitio virtual Nginx

🛠️📄 **Crear archivo de configuración :**

```bash
   > sudo nano /etc/nginx/sites-available/mi_sitio.conf
```

⚙️ **Ejemplo de configuración básica :**

```conf
server {
      listen 80;
      listen [::]:80;
      root /var/www/mi_sitio;
      index index.html index.htm index.nginx-debian.html;
      server_name mi_sitio;
      location / {
            try_files $uri $uri/ =404;
      }
   }
```

🔌 **Habilitar el sitio :**

```bash
   > sudo ln -s /etc/nginx/sites-available/mi_sitio /etc/nginx/sites-enabled/
   > sudo systemctl reload nginx
```
---

### Comprobación del funcionamiento

Si no tenemos un servidor DNS, debemos configurar manualmente nuestro cliente para que pueda acceder al sitio virtual de dos formas:

- Si tenemos acceso a un usuario administrador _(sudo)_, podemos modificar el archivo _`/etc/hosts`_ y añadir la siguiente linea: _`IP_SERVIDOR  mi_sitio`_.
> 

- Si no tenemos acceso al usuario administrador, podemos lanzar nuestra navegador _(en incognito para evitar caché)_ desde terminal con un parámetro que indique el servidor DNS que quieres que aplique con el siguiente comando:
   **Linux :** _`google-chrome --incognito --host-resolver-rules="MAP mi-sitio IPSERVER" --ignore-certificate-error`_.
   **Windows :** _`& "C:\Program Files\Google\Chrome\Application\chrome.exe" --incognito --host-resolver-rules="MAP mi_sitio localhost" --ignore-certificate-errors`_.

✅

---
## 🛡️ Autenticación Básica

🔑 **Crear archivo de contraseñas :**

```bash
   > sudo apt install apache2-utils
   > sudo htpasswd -c /etc/nginx/.htpasswd usuario
   > sudo mkdir /var/www/mi_sitio/areaprivada
```

⚙️ **Configurar autenticación :**

```conf
server {
   listen 80;
   listen [::]:80;
   root /var/www/mi_sitio;
   index index.html index.htm index.nginx-debian.html;
   server_name mi_sitio;
   
   location /areaprivada/ {
         auth_basic "Acceso Restringido";
         auth_basic_user_file /etc/nginx/.htpasswd;
         try_files $uri $uri/ =404;
   }
}
```

## 🚧 Autentificación por IP

⚙️ **Configurar autenticación :**

> El orden de **`allow`** y **`deny`** es muy importante en **Nginx**, ya que se evalúan en el mismo orden en que aparecen.

```conf
server{
   #Configuración anterior

   location /areaprivada/ {
        satisfy all;
        deny 172.1.10.6;
        allow 172.1.10.15;
        allow 172.1.3.14;
        deny all;
        auth_basic "Acceso Restringido";
        auth_basic_user_file /etc/nginx/.htpasswd;
        root /var/www/mi_sitio;
   }
}
```

♻️ **Reiniciar el servicio :**

```bash
   > sudo systemctl reload nginx
```

## ✅🔒 Activar HTTPS

```bash
   > sudo apt install openssl
```

📜 **Crear certificado autofirmado :**

```bash
 > sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt
```

⚙️ **Configurar HTTPS :**

```conf
server {
   listen 443 ssl;
   server_name mi_sitio;
   ssl_certificate /etc/nginx/ssl/server.crt;
   ssl_certificate_key /etc/nginx/ssl/server.key;
   root /var/www/mi_sitio;
   index index.html;
}
```

🔐🌐➡️ **Forzar redirección a HTTPS :**

```Conf
server {
   listen 80;
   server_name mi_sitio;
   access_log /var/log/nginx/http_access.log;
   return 301 https://$host$request_uri;
}
```

♻️ **Reiniciar el servicio :**

```bash
   > sudo systemctl reload nginx
```

---
---

## ![Apache Logo](https://www.apache.org/img/support-apache.jpg) Apache2

### 🚀 Instalación y Configuración Básica 🚀

📦 **Instalación en Ubuntu o Debian :**

```bash
   > sudo apt update && sudo apt install apache2
```

🔌 **Habilitar y verificar servicio:**

```bash
   > sudo systemctl enable apache2
   > sudo systemctl start apache2
   > sudo systemctl status apache2
```

### 📂 Directorios esenciales de Apache 📂

| **📂 Directorio**                    | **📝 Descripción**                                                                |
|--------------------------------------|----------------------------------------------------------------------------------|
| `/etc/apache2`                       | Directorio de configuración de Apache2.                                         |
| `/var/log/apache2`                   | Directorio donde se guardan los registros de Apache2.                           |
| `/var/www/html`                      | Directorio donde se alojan los contenidos de nuestra web.                       |
| `/etc/apache2/sites-available`       | Configuración de sitios web disponibles.                                        |
| `/etc/apache2/sites-enabled`         | Directorio donde se alojan las webs que tenemos habilitadas.                    |


### Gestión de Permisos

➡️ [Gestión de Permisos](#gestión-de-permisos)

### Configurar un sitio virtual Apache2

🛠️📄 **Crear archivo de configuración :**

```bash
   > sudo nano /etc/apache2/sites-available/mi_sitio.conf 
```

⚙️ **Ejemplo de configuración básica :**

```conf
<VirtualHost *:80>
   ServerName mi_sitio
   ServerAlias www.mi_sitio.com mi_sitio.local
   DocumentRoot /var/www/mi_sitio
   <Directory /var/www/mi_sitio>
      AllowOverride All
      Require all granted
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/mi_sitio_error.log
   CustomLog ${APACHE_LOG_DIR}/mi_sitio_access.log combined
</VirtualHost>
```

🔌 **Habilitar el sitio :**

```bash
   > sudo a2ensite mi_sitio.conf
   > sudo systemctl reload apache2
```
---

### Comprobación del funcionamiento

✅ ➡️ [Comprobación del funcionamiento](#comprobación-del-funcionamiento)

---

## 🛡️ Autenticación Básica

🔑 **Crear archivo de contraseñas :**

```bash
   > sudo apt install apache2-utils
   > sudo htpasswd -c /etc/apache2/.htpasswd usuario
   > sudo mkdir /var/www/mi_sitio/areaprivada
```

⚙️ **Configurar autenticación :**

```conf
<Directory "/var/www/mi_sitio/areaprivada">
    AuthType Basic
    AuthName "Acceso Restringido"
    AuthBasicProvider file
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```

## 🚧 Autentificación por IP

⚙️ **Configurar autenticación :**

```conf
<Directory "/var/www/mi_sitio/areaprivada">
    AuthType Basic
    AuthName "Restricted Area"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
    Order allow,deny
    Allow from 172.1.10.15 172.1.3.14
    Deny from all
</Directory>
```

♻️ **Reiniciar el servicio :**

```bash
   > sudo systemctl reload apache2
```

---

## ✅🔒 Activar HTTPS

```bash
   > sudo apt install openssl
```

📜 **Crear certificado autofirmado :**
```bash
   > sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/server.key -out /etc/apache2/ssl/server.crt
```

⚙️ **Configurar HTTPS :**
```conf
<VirtualHost *:443>
   SSLEngine on
   SSLCertificateFile /etc/apache2/ssl/apache.crt
   SSLCertificateKeyFile /etc/apache2/ssl/apache.key
   DocumentRoot /var/www/mi_sitio
   ServerName mi_sitio
   <Directory "/var/www/mi_sitio">
      Options Indexes FollowSymLinks
      AllowOverride All
      Require all granted
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/mi_sitio_ssl_error.log
   CustomLog ${APACHE_LOG_DIR}/mi_sitio_ssl_access.log combined
</VirtualHost>
```

🔐🌐➡️ **Forzar redirección a HTTPS :**
```diff
<VirtualHost *:80>
  Redirect "/" "https://mi_sitio/"
</VirtualHost>
``` 

#### ✔️ Habilitar módulos necesarios y reiniciar Apache2
```bash
   > sudo a2enmod ssl
   > sudo a2enmod rewrite
   > sudo systemctl restart apache2
```

## Pila LAMP 🔥🐧🐬🐘

📦 **Instalar componentes :**

```bash
   > sudo apt update
   > sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql
```

⚙️ **Configurar base de datos :**

```bash
   > sudo mysql_secure_installation
```

✅ **Probar PHP :**

> Crear un archivo en _`/var/www/html/info.php`_ con:
   >```php
     ><?php phpinfo(); ?>
   >```
>Acceder desde el navegador: _`http://<IP-DEL-SERVIDOR>/info.php`_.

---

###  Docker Compose 🐳➕⚙️

>Para configurar una pila LAMP usando Docker Compose :

```yaml
   version: '3'
   services:
     apache:
       image: httpd:latest
       ports:
         - "80:80"
       volumes:
         - ./website:/usr/local/apache2/htdocs/
     php:
       image: php:7.4-apache
     mysql:
       image: mysql:5.7
       environment:
         MYSQL_ROOT_PASSWORD: secret
         MYSQL_DATABASE: mydb
         MYSQL_USER: user
         MYSQL_PASSWORD: password
   volumes:
     mysql-data:
```

## phpMyAdmin 🐘📊

📦 **Instalación :**
```bash
   > sudo apt-get install phpmyadmin
```

🔓 **Acceso**:
> Puedes acceder a phpMyAdmin desde _`http://IP/phpmyadmin`_ para gestionar las bases de datos de manera más sencilla desde una interfaz web.
---