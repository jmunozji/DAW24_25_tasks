---
title: 'Ch3 - DMP'
---
# 📝 UD3 - Servidores web - Cheatsheet

> David Maratrat Pons

## 🧑‍💻 Comandos Nginx

| Comando                   | Descripción                                       |
|---------------------------|---------------------------------------------------|
| `systemctl start nginx`   | `Inicia el servidor web nginx`                    |
| `systemctl stop nginx`    | `Detiene el servidor web nginx`                   |
| `systemctl restart nginx` | `Reinicia el servidor web nginx`                  |
| `systemctl reload nginx`  | `Recarga la configuración del servidor web nginx` |
| `nginx -t`                | `Prueba la configuración del servidor web nginx`  |

### 🌐 Sitios Virtuales en Nginx
Los sitios virtuales en Nginx se configuran a través de bloques server. 
Cada bloque puede representar un sitio web independiente, configurando directivas como el puerto de escucha,
el nombre del servidor (server_name) y la ruta del contenido (root).
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

### 🔒 Configuración SSL en Nginx

Para habilitar SSL en Nginx, debes asegurarte de que el servidor escuche en el puerto 443
y configurar las directivas ssl_certificate y ssl_certificate_key con la ruta de tus certificados.

```
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/ssl/certs/server.crt;
    ssl_certificate_key /etc/ssl/private/server.key;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

### 📑 Ejemplo completo de nginx.conf
```
worker_processes auto;

events {
    worker_connections 1024;
}

http {
    # Habilitar compatibilidad con HTTPS
    server {
        listen 80;
        server_name example.com;
        return 301 https://$host$request_uri; # Redirección a HTTPS
    }

    server {
        listen 443 ssl;
        server_name example.com;

        root /var/www/example_com;
        index index.html;

        ssl_certificate /etc/ssl/certs/example_com.crt;
        ssl_certificate_key /etc/ssl/private/example_com.key;

        error_log /var/log/nginx/example_com_error.log;
        access_log /var/log/nginx/example_com_access.log;

        location / {
            index index.html;
        }
    }

    server {
        listen 80;
        server_name example.org;
        return 301 https://$host$request_uri; # Redirección a HTTPS
    }

    server {
        listen 443 ssl;
        server_name example.org;

        root /var/www/example_org;
        index index.html;

        ssl_certificate /etc/ssl/certs/example_org.crt;
        ssl_certificate_key /etc/ssl/private/example_org.key;

        error_log /var/log/nginx/example_org_error.log;
        access_log /var/log/nginx/example_org_access.log;

        location /app {
            index index.html;
        }
    }
}

```

---

## 🧑‍💻 Comandos Apache2

| Comando                     | Descripción                                                    |
|-----------------------------|----------------------------------------------------------------|
| `systemctl start apache2`   | `Inicia el servicio Apache2.`                                  |
| `systemctl stop apache2`    | `Detiene el servicio Apache2.`                                 |
| `systemctl restart apache2` | `Reinicia el servicio Apache2.`                                |
| `systemctl reload apache2`  | `Recarga la configuración de Apache2 sin detener el servicio.` |
| `apachectl configtest`      | `Prueba la configuración de Apache2.`                          |

### 🌐 Sitios Virtuales en Apache

En Apache, los sitios virtuales se configuran usando el bloque <VirtualHost>,
que permite especificar configuraciones específicas por cada dominio o dirección IP.
Puedes configurar múltiples sitios en un solo servidor, cada uno con su propio archivo de configuración
o mediante el uso de archivos de configuración dentro del directorio sites-available y sites-enabled.

```
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/example
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### 🔒 Configuración SSL en Apache

En Apache, para habilitar SSL debes activar el módulo ssl y configurar el bloque <VirtualHost>
para usar el puerto 443 junto con las directivas de SSL SSLCertificateFile y SSLCertificateKeyFile.

```
<VirtualHost *:443>
    ServerName example.com

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/server.crt
    SSLCertificateKeyFile /etc/ssl/private/server.key

    DocumentRoot /var/www/html
    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### 📑 Ejemplo completo de apache2.conf
```
# Sitio Virtual 1 - example.com
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html

    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

    # Redirección a HTTPS
    Redirect permanent / https://example.com/

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:443>
    ServerName example.com

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/server.crt
    SSLCertificateKeyFile /etc/ssl/private/server.key

    DocumentRoot /var/www/html
    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

# Sitio Virtual 2 - example.org
<VirtualHost *:80>
    ServerName example.org
    DocumentRoot /var/www/example

    <Directory /var/www/example>
        AllowOverride All
        Require all granted
    </Directory>

    # Redirección a HTTPS
    Redirect permanent / https://example.org/

    ErrorLog ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log combined
</VirtualHost>

<VirtualHost *:443>
    ServerName example.org

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/example_org.crt
    SSLCertificateKeyFile /etc/ssl/private/example_org.key

    DocumentRoot /var/www/example
    <Directory /var/www/example>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/example_error.log
    CustomLog ${APACHE_LOG_DIR}/example_access.log combined
</VirtualHost>
```

---

## 📄 Ejemplo Dockerfile enfocado a servidores web

Define la imagen de un contenedor, incluyendo el sistema base, configuraciones y archivos necesarios.

```
# Dockerfile para un servidor Nginx
FROM nginx:latest

# Copiar archivo de configuración personalizado
COPY nginx.conf /etc/nginx/nginx.conf

# Copiar contenido de la web
COPY ./html /usr/share/nginx/html

# Exponer el puerto
EXPOSE 80

# Iniciar el servidor
CMD \["nginx", "-g", "daemon off;"]
```

---

## 📄 docker-compose.yml enfocado a servidores web

Configura y orquesta múltiples contenedores, estableciendo servicios, redes y volúmenes.

```
version: '3.9'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./html:/usr/share/nginx/html
    networks:
      - webnet
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: appdb
      MYSQL_USER: appuser
      MYSQL_PASSWORD: apppass
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - webnet
networks:
  webnet:
    driver: bridge
volumes:
  db_data:
```