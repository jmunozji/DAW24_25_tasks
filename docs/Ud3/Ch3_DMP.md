---
title: 'Ch3 - DMP'
---
# 📝 UD3 - Servidores web - Cheatsheet

> David Maratrat Pons

## 🧑‍💻 Comandos Nginx

| Comando                   | Descripción                                       |
|---------------------------|---------------------------------------------------|
| `systemctl start nginx`   | `Arranca el servidor web nginx`                   |
| `systemctl stop nginx`    | `Para el servidor web nginx`                      |
| `systemctl restart nginx` | `Reinicia el servidor web nginx`                  |
| `systemctl -s reload`     | `Recarga la configuración del servidor web nginx` |
| `systemctl -t`            | `Prueba la configuración del servidor web nginx`  |

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
    server {
        listen 80;
        server_name example.com;
        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
        location /api/ {
            proxy_pass http://backend:5000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

## 🧑‍💻 Comandos Apache2

| Comando                     | Descripción                                                        |
|-----------------------------|--------------------------------------------------------------------|
| `systemctl start apache2`   |                                                                    |
| `systemctl stop apache2`    |                                                                    |
| `systemctl restart apache2` |                                                                    |
| `systemctl reload apache2`  |                                                                    |
| `apachectl configtest`      |                                                                    |

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
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html

    <Directory \/var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

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