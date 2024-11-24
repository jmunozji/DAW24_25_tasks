---
title: 'CH3 - MMC'
---

# CheatSheet Servidor Web Marcel Mañas

Tipo: Apuntes
Estado: En proceso
Tema: Despliegue

## Instalación servidor web

| sudo apt install [nginx o apache2] | Instalar servidor web |
| --- | --- |

## Comprobar registros

| cat /var/log/[nginx o apache2]/access.log | Comprobación de solicitudes |
| --- | --- |
| cat /var/log/[nginx o apache2]/error.log | Comprobación de errores |

## Dockerización

- Dockerfile para instalar nginx o apache2:
    
    ```docker
    FROM debian:latest
    MAINTAINER Nombre Apellido <correo_electrónico>
    
    # Actualizamos e instalamos Nginx
    RUN apt update && apt upgrade -y && apt install -y [nginx o apache2] && \
        rm -rf /var/lib/apt/lists/*
    
    # Exponemos el puerto 80
    EXPOSE 80
    
    # Comando para iniciar Nginx en primer plano
    CMD ["nginx", "-g", "daemon off;"] o
    CMD ["apache2ctl", "-D", "FOREGROUND"]
    ```
    

| mkdir -p ~/web | Creación de directorio para almacenar nuestras páginas web |
| --- | --- |
- Creación de fichero index.html (página web)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Servidor dockerizado!</title>
    <style>
    html { color-scheme: light dark; }
    body { width: 35em; margin: 0 auto;
    font-family: Tahoma, Verdana, Arial, sans-serif; }
    </style>
    </head>
    <body>
    <h1>Bienvendido al servidor web dockerizado!</h1>
    <p> Este es un servidor web dockerizado </p>
    </body>
    </html>
    ```
    

| docker run -d --name=miservernginx --mount\
type=bind,source=/home/admin/web,target=/var/www/html \
-p 80:80 \
servernginx | Creación del contenedor con la página web anterior. |
| --- | --- |

## Sitios web virtuales

| nano /var/www/sitio1/index.html | Creación de la página web virtual |
| --- | --- |

```html
<!DOCTYPE html>
<html>
<head>
<title>Sitio1!</title>
</head>
<body>
<h1>Bienvendido al sitio1!</h1>
<p> Este es el sitio1 de usuario1</p>
</body>
</html>
```

### Configuración sitio web

| sudo nano /etc/nginx/sites-available/sitio1 | Acceso a la configuración de la página anterior |
| --- | --- |

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

| sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/
ls -la /etc/nginx/sites-enabled/ | Creación de archivo simbólico. |
| --- | --- |
| sudo systemctl restart nginx | Reinicio del servidor |
| google-chrome --host-resolver-rules="MAP sitio1 IPSERVER" --ignore-certificate-error | Para comprobar que funciona la página web |

### Auntentificación

| sudo apt install apache2-utils | Instalación de paquete para usar htpasswd |
| --- | --- |
| sudo htpasswd -c /etc/nginx/.htpasswd profe | Crea el archivo que guarda los usuarios y contraseñas, además crea al usuario “profe” |
| sudo htpasswd /etc/nginx/.htpasswd otro_usuario | Crea usuarios para el archivo |
| sudo nano /etc/nginx/sites-available/sitio1 | Modificar la configuración:  |

```bash
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

- Para que sea una autentificación en una página en específico:
    
    ```bash
    server {
        listen 80;
        server_name sitio1.com;
    
        root /var/www/sitio1;
    
        location / {
            try_files $uri $uri/ =404;
        }
    
        location /intranet {
            auth_basic "Área restringida";
            auth_basic_user_file /etc/nginx/.htpasswd;
            try_files $uri $uri/ =404;
        }
    }
    ```
    
- Autentificar mediante IP:
    
    ```bash
    server {
        listen 80;
        server_name sitio1.com;
    
        root /var/www/sitio1;
    
        location / {
            try_files $uri $uri/ =404;
        }
    
        location /intranet {
            try_files $uri $uri/ =404;
        }
        
        location /api {
    	    #..
    	    satisfy all;
    	    
    	    deny 192.168...
    	    allow 192.168...
    	    allow 127.0...
    	    deny all;
        }
    }
    ```
    
    | allow 192.168… | Garantiza acceso |
    | --- | --- |
    | deny 127.0… | Deniega acceso |
    | satisfy all/any | **all**: debe cumplir todas las condiciones
    **any:** Basta con cumplir una de las condiciones |

### Autentificación mediante HTTPS

| sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt | Creación del certificado y las claves de autentificación.  |
| --- | --- |
- Modificación de la configuración de la página:

```bash
server {
          listen 443 ssl;
				  ssl_certificate /etc/nginx/ssl/server.crt;
				  ssl_certificate_key     /etc/nginx/ssl/server.key;
				  ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
				  ssl_protocols TLSv1.2 TLSv1.3;
				  access_log /var/log/nginx/https_access.log;
        }
}
```

## Sitios virtuales en servidor dockerizado

- Estructura de directorios:
    
    ```docker
    ~
    ├── sitiosvirtuales/
    │   ├── html
    │   │   └── index.html
    │   └── sitio1
    │       └── index.html
    └── nginxvirtual
        ├── Dockerfile
        ├── docker-compose.yaml
        ├── entrypoint.sh
        └── sites-available
            ├── default
            └── sitio1
    ```
    
- Contenido docker-compose.yaml:
    
    ```bash
    services:
        nginx:
            build: .
            volumes:
                - ../sitiosvirtuales:/var/www
                - ./sites-available:/etc/nginx/sites-available
            ports:
                - 80:80
    ```
    
- Contenido Dockerfile:
    
    ```docker
    FROM debian:latest
    MAINTAINER Nombre Apellido <correo_electrónico>
    
    # Actualizamos e instalamos Nginx
    RUN apt update && apt upgrade -y && apt install -y [nginx o apache2] && \
        rm -rf /var/lib/apt/lists/*
    
    # Copiar el script de arranque
    COPY entrypoint.sh /entrypoint.sh
    
    # Hacer que sea ejecutable
    RUN chmod +x /entrypoint.sh
    
    # Exponemos el puerto 80
    EXPOSE 80
    
    # Establecer el script como el entrypoint
    ENTRYPOINT ["/entrypoint.sh"]
    ```
