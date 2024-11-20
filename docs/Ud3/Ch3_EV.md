## **Actualizar y preparar el sistema**

- Actualizar repositorios y sistema
: `sudo apt-get update && sudo apt-get upgrade`

## **Servidores Web**
### Apache

- Instalar Apache
: `sudo apt install apache2`
- Verificar estado
: `sudo systemctl status apache2`
- Activar y arrancar
```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```
- Ubicación de archivos web
: `/var/www/html`
- Acceso en navegador
: `http://<IP-servidor>`

### Nginx

- Instalar Nginx
: `sudo apt install nginx`
- Verificar el estado
: `systemctl status nginx`

Logs
- Accesos
: `/var/log/nginx/access.log`
- Errores
: `/var/log/nginx/error.log`

- Acceso en navegador
: `http://<IP-servidor>`

## **Bases de datos**
### MariaDB

- Instalar MariaDB
: `sudo apt install mariadb-server`
- Verificar estado
: `sudo systemctl status mysql`
- Configurar seguridad
: `sudo mysql_secure_installation`

Respuestas clave
- Cambiar contraseña root
: `Y`
- Contraseña
: `ieselcaminas`

- Conectar
: `mysql -u root -p`

## **PHP**

- Instalar PHP y módulos
: `sudo apt-get install php libapache2-mod-php php-mysql`
- Reiniciar Apache
: `sudo systemctl restart apache2`
- Archivo de prueba
: `sudo nano /var/www/html/info.php`
- Contenido
```bash
<?php
phpinfo();
?>
```
- Acceso navegador
: `http://<IP-servidor>/info.php`

## **Gestión gráfica de Bases de Datos con phpMyAdmin**

- Instalar phpMyAdmin
: `sudo apt-get install phpmyadmin`

Respuestas clave
- Servidor
: `apache2`
- Configuración con `dbconfig-common`: Sí
- Contraseña root
: `ieselcaminas`

- Crear enlace simbólico si no se accede
: `sudo ln -s /usr/share/phpmyadmin/ /var/www/html/phpmyadmin`
- Acceso en navegador
: `http://<IP-servidor>/phpmyadmin`

## **Sitios Virtuales**

Configuración básica de permisos
- Archivos
: `sudo chmod 644 /var/www/html/index.html`
- Carpetas
: `sudo chmod 755 /var/www/html`
- Propietario
: `sudo chown -R www-data:www-data /var/www/html`

### Nginx

- Crear archivo de configuración
: `sudo nano /etc/nginx/sites-available/sitio1`
- Contenido
```bash
server {
    listen 80;
    root /var/www/sitio1;
    index index.html;
    server_name sitio1;
    location / {
        try_files $uri $uri/ =404;
    }
}
```
- Activar sitio
```bash
sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### Apache

- Crear archivo de configuración
: `sudo nano /etc/apache2/sites-available/sitio1.conf`
- Contenido
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
- Activar sitio
```bash
sudo a2ensite sitio1.conf
sudo systemctl restart apache2
```

## **HTTPS**
### Nginx

- Crear contenidos
```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt
```
- Configurar HTTPS
```bash
server {
    listen 443 ssl;
    ssl_certificate /etc/nginx/ssl/server.crt;
    ssl_certificate_key /etc/nginx/ssl/server.key;
}
```
- Redirigir HTTP a HTTPS
```bash
server {
    listen 80;
    return 301 https://$host$request_uri;
}
```
- Reiniciar servicio
: `sudo systemctl restart nginx`

### Apache

- Activar módulo SSL
```bash
sudo a2enmod ssl
sudo systemctl restart apache2
```
- Crear certificados
```bash
sudo mkdir -p /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/apache2/ssl/server.key -out /etc/apache2/ssl/server.crt
```
- Configurar HTTPS
```bash
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/server.crt
    SSLCertificateKeyFile /etc/apache2/ssl/server.key
</VirtualHost>
```
- Redirigir HTTP a HTTPS
```bash
<VirtualHost *:80>
    Redirect permanent / https://sitio1/
</VirtualHost>
```
- Reiniciar servicio
: `sudo systemctl restart apache2`