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

<details>
<summary>Ver ejemplo de nginx.conf</summary>
<pre>
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
</pre>
</details>

---

## 🧑‍💻 Comandos Apache2

| Comando                     | Descripción                                                        |
|-----------------------------|--------------------------------------------------------------------|
| `systemctl start apache2`   |                                                                    |
| `systemctl stop apache2`    |                                                                    |
| `systemctl restart apache2` |                                                                    |
| `systemctl reload apache2`  |                                                                    |
| `apachectl configtest`      |                                                                    |

<details>
<summary>Ver ejemplo de apache2.conf</summary>
<pre>
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html

    <Directory \/var/www/html>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
<\/VirtualHost>

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
</pre>
</details>

---

## 📄 Ejemplo Dockerfile enfocado a servidores web

Define la imagen de un contenedor, incluyendo el sistema base, configuraciones y archivos necesarios.

<details>
<summary>Ver ejemplo de Dockerfile</summary>
<pre>
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
</pre>
</details>

---

## 📄 docker-compose.yml enfocado a servidores web

Configura y orquesta múltiples contenedores, estableciendo servicios, redes y volúmenes.

<details>
<summary>Ver ejemplo de docker-compose.yml</summary>
<pre>
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
</pre>
</details>