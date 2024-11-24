---
title: 'Ch3 - ACF'
---

# 🌐 Nginx y Apache Cheatsheet

### 1. 🛠️ Comandos de instalación y comprobación de estado

| Comando | Explicación |
|----------|----------|
| `apt-install nginx` | 🖥️ Comando para instalar Nginx |
| `apt-install apache2` | 🖥️ Comando para instalar Apache |
| `system status nginx` | 🚦 Comprobar el estado de nginx |
|`sudo systemctl restart nginx`| 🔄 Reiniciar el servicio de nginx|


### 2. 🚀 Sitios virtuales

| Comando | Explicación |
|----------|----------|
| `sudo chown -R www-data:www-data /var/www/html` | 🔧 Cambiar el propietario del directorio |
|`sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/`| 🌀 Creación de archivo simbólico|
|`google-chrome --host-resolver-rules="MAP midominio.com 123.45.67.89, MAP otrodominio.com 98.76.54.32" --ignore-certificate-errors`|🌍 Si no tienes permisos en tu máquina host puedes lanzar chrome pasándole las DNS|

📝 Ejemplo de archivo de configuración del bloque server en nginx:
```
server {
    listen 80;
    server_name example.com;
    root /var/www/example;
    location / {
        index index.html;
    }
    error_log /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
}
```
📝 Ejemplo de archivo de configuración en Apache2
```
<VirtualHost *:80>
        ServerName sitiovirtual
        DocumentRoot /var/www/sitiovirtual
        <Directory /var/www/sitiovirtual>
                AllowOverride All
                Require all granted
        </Directory>
</VirtualHost>
```


### 3. 🔒 Autenticación

| Comando | Explicación |
|----------|----------|
| `sudo apt install apache2-utils` |🛠️ Instala utilidades para poder usar htpasswd para crear contraseñas |
| `sudo htpasswd -c /etc/nginx/.htpasswd profe` |🔑 Creación de archivo oculto y incluye el primer usuario y contraseña para profe |

📝 Ejemplo de archivo de configuración con autenticación en nginx:
```
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
📝 Ejemplo de archivo de configuración con autenticación en apache2:
```
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
### 4. 📂 Activación HTTPS

| Comando | Explicación |
|----------|----------|
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt` |🔐 Crear certificado y claves |

📝 Ejemplo de configuración SSL en nginx:
```
  listen 443 ssl;
  ssl_certificate /etc/nginx/ssl/server.crt;
  ssl_certificate_key     /etc/nginx/ssl/server.key;
  ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
  ssl_protocols TLSv1.2 TLSv1.3;
  access_log /var/log/nginx/https_access.log;
```
📝 Ejemplo de redirección forzosa en nginx:
```
server {
        listen 80;
        server_name sitio1;
        access_log /var/log/nginx/http_access.log;
        return 301 https://sitio1$request_uri;
}
```
📝 Ejemplo de configuración SSL en apache2:
```
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
📝 Ejemplo de redirección forzosa en apache2:
```
<VirtualHost *:80>
    ServerName sitio1
    Redirect permanent / https://sitio1/
</VirtualHost>
```

### 5.⚙️ Instalación pila LAMP

| Comando | Explicación |
|----------|----------|
| `sudo apt install mariadb-server` |🛢️ Instalación de MariaDB|
|`sudo mysql_secure_installation`|🔒 Configuración de MariaDB|
|`sudo apt-get install php libapache2-mod-php php-mysql`|💻 Instalación de Php|
|`sudo apt-get install phpmyadmin`|📊 Instalación de phpMyAdmin|


