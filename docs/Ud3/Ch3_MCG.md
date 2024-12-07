---
title: 'CH3 - MCG'
---
# cheatsheet 3 - servidores web

## Instalaciones varias

### instala tu server de preferencia

```bash
sudo apt install nginx
```
```bash
sudo apt install apache2
```
```bash
sudo apt install apache2utils
```

### instala la pila LAMP

```bash
sudo apt install mariadb-server
```
```bash
sudo apt-get install php libapache2-mod-php php-mysql
```
```bash
sudo apt-get install phpmyadmin
```

## comprobación de servicios con systemctl

```bash
systemctl status $serverInstalado
systemctl start $serverInstalado
systemctl stop $serverInstalado
systemctl restart $serverInstalado
systemctl disable $serverInstalado
```

## Ruta a archivos de configuración

```bash
/etc/$serverInstalado/sites-available/
```

## Comandos útiles

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt  # OJO CON LAS RUTAS DE SALIDA.
```
```bash
sudo a2ensite $nombreSitio
```
```bash
sudo a2enmod $nombreSitio
```
```bash
google-chrome --host-resolver-rules="MAP $nombreSitio $direccionIP" --ignore-certificate-error
```
```bash
sudo mysql_secure_installation
```

## Ejemplos de archivos de configuración

### nginx

```nginx
server {
    listen 80;
    server_name $nombreServer;
    access_log /var/log/nginx/http_access.log;
    return 301 https://$nombreServer$request_uri;
}

server {
    listen 443 ssl;
    server_name $nombreServer;
    access_log /var/log/nginx/http_access.log;

    ssl_certificate /etc/nginx/ssl/server.crt;
    ssl_certificate_key     /etc/nginx/ssl/server.key;
    ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
    ssl_protocols TLSv1.2 TLSv1.3;
    access_log /var/log/nginx/https_access.log;
}
```

### apache2

```apache
<VirtualHost *:80>
    ServerName $nombreServer
    Redirect permanent / https://$nombreServer/
</VirtualHost>

<VirtualHost *:443>
    ServerName $nombreServer
    DocumentRoot $directorioDeLaPagina

    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/server.crt
    SSLCertificateKeyFile /etc/apache2/ssl/server.key

    <Directory $directorio>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

(la verdad no sabía con que más rellenar esto :|)
