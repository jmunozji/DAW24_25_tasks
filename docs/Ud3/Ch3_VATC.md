---

title: 'CH3 - VATC'

---

# 🌟 Configuración de Servidores Web

> ✍️ **Victor Alexandru Teleanu Covaci**

## **🌐 Instalación de Servidores**

| **💻 Servidor Web / 🛠️ Comando**             | **📝 Descripción**                           |
|-----------------------------------------------|----------------------------------------------|
| **📡 Nginx**                                  |                                              |
| `sudo apt-get update && sudo apt-get upgrade` | Actualizar paquetes antes de instalar Docker |
| `sudo apt install nginx`                      | Instala Nginx                                |
| **🔗 Apache2**                                |                                              |
| `sudo apt-get update && sudo apt-get upgrade` | Actualizar paquetes antes de instalar Docker |
| `sudo apt install apache2`                    | Instala Apache                               |

## 📊 **Directorios Importantes**

| **💻 Servidor Web / 🛠️ Comando** | **📝 Descripción**                  |
|-----------------------------------|-------------------------------------|
| **📡 Nginx**                      |                                     |
| `/etc/nginx`                      | Configuración principal de Nginx    |
| `/etc/nginx/sites-available/`     | Configuración de sitios disponibles |
| `/etc/nginx/sites-enabled/`       | Configuración de sitios habilitados |
| `/var/log/nginx`                  | Registros de Nginx                  |
| **🔗 Apache2**                    |                                     |
| `/etc/apache2`                    | Configuración principal de Apache   |
| `/etc/apache2/sites-available/`   | Configuración de sitios disponibles |
| `/etc/apache2/sites-enabled/`     | Configuración de sitios habilitados |
| `/var/log/apache2`                | Registros de Apache                 |
| **📶 Común**                      |                                     |
| `/etc/apache2`                    | Contenido web                       |

## 🛠️ **Configuración Básica**

### 📄 Crear un sitio web 

- Con `sudo mkdir /var/www/sitio1` creamos el directorio de nuestra web.
- Con `sudo touch /var/www/sitio1/index.html` creamos el archivo index.html.
- Con `sudo chown -R www-data:www-data /var/www/sitio1` cambiamos el propietario y grupo.

### 📑 **Configurar Nginx**

- Creamos el archivo de configuración usando `sudo nano /etc/nginx/sites-available/sitio1`.

#### 🗂️ Contenido

    server {
        listen 80;
        root /var/www/sitio1;
        index index.html;
        server_name sitio1;
    
        location / {
            try_files $uri $uri/ =404;
        }
    }   

- Con `sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/` habilitamos el sitio web.
- Y reiniciamos el servicio mediante `sudo systemctl restart nginx`.

### 📑 **Configurar Apache2**

- Creamos el archivo de configuración usando `sudo nano /etc/apache2/sites-available/sitio1.conf`. 

#### ⚠️ Importante

En apache los ficheros de configruación acaban en .conf.

#### 🗂️ Contenido

    <VirtualHost *:80>
        ServerName sitiovirtual
        DocumentRoot /var/www/sitiovirtual
        <Directory /var/www/sitiovirtual>
                AllowOverride All
                Require all granted
        </Directory>
    </VirtualHost>

- Con `sudo a2ensite sitio1.conf` habilitamos el sitio web.
- Y reiniciamos el servicio mediante `sudo systemctl reload apache2`.

## 🛡️ **Seguridad y SSL**

### 🔒 Generar Certificado SSL

- Con `sudo mkdir /etc/nginx/ssl` creamos el directorio para la creación del certificado.
- Con `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crtl` creamos la clave.

### 🔑 Configurar HTTPS Nginx

- `sudo nano /etc/nginx/sites-available/sitio1`.

#### 🗂️ Contenido

```diff
server {
+       listen 443 ssl;
+       ssl_certificate /etc/nginx/ssl/server.crt;
+       ssl_certificate_key /etc/nginx/ssl/server.key;
+       ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
+       ssl_protocols TLSv1.2 TLSv1.3;
+       access_log /var/log/nginx/https_access.log;

    root /var/www/sitio1;
    index index.html;
    server_name sitio1;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 🔄 Redirección a HTTPS

```diff
server {
+   listen 80;
+   server_name sitio1;
+   return 301 https://$host$request_uri;
}
```

- Reiniciamos Nginx con `sudo systemctl restart nginx`.

### 🔑 Configurar HTTPS Apache

```diff
<VirtualHost *:443>
    ServerName sitio1
    DocumentRoot /var/www/sitio1

+       #Activa SSL
+       SSLEngine on
+       # Especifica la ruta a tu certificado SSL y clave privada
+       SSLCertificateFile /etc/apache2/ssl/server.crt
+       SSLCertificateKeyFile /etc/apache2/ssl/server.key

    <Directory /var/www/sitio1>
            AllowOverride All
            Require all granted
    </Directory>
</VirtualHost>
```

### 🔄 Redirección a HTTPS

```diff
<VirtualHost *:80>
+       ServerName sitio1
+       Redirect permanent / https://sitio1/
</VirtualHost>
```

### ♻️ Habilitar módulos necesarios y reiniciar Apache

- `sudo a2enmod ssl` habilitará el módulo SSL para habilitar conexiones HTTPS.
- `sudo a2enmod rewrite` habilita el módulo de reescritura de URL para permitir la manipulación de direcciones web.
- `sudo systemctl restart apache2` reinicia el servicio.

## 🚪 **Autenticación de Usuarios**

### 🔐 Habilitar autenticación

- Creamos el usuario con `sudo apt install apache2-utils` y `httpasswd -c /etc/nginx/.htpasswd usuario`.

### 🗂️ Configurar autenticación Nginx

```diff
server {
    listen 80;
    listen [::]:80;
    root /var/www/sitio1;
    index index.html index.htm index.nginx-debian.html;
    server_name sitio1;
    location / {
+           auth_basic "Área restringida";
+           auth_basic_user_file /etc/nginx/.htpasswd;
        try_files $uri $uri/ =404;
    }
}
```

### 🗂️ Configurar autenticación Apache2

```diff
<VirtualHost *:80>
    ServerName sitio1
    DocumentRoot /var/www/sitio1
    <Directory /var/www/sitio1>
            AllowOverride All
            Require all granted
    </Directory>
+       <Directory /var/www/sitio1/intranet>
+               AuthType Basic
+               AuthName "Intranet"
+               AuthBasicProvider file
+               AuthUserFile /etc/apache2/.htpasswd
+               Require valid-user
+       </Directory>
</VirtualHost>
```

### ♻️ Reiniciamos los servicios

- Con `sudo systemctl reload apache2` reiniciamos apache.
- Con `sudo systemctl reload nginx` reiniciamos nginx.


## 🔥 **Pila LAMP**

### 💾 Instalación de componentes

- Usamos `sudo apt update && sudo apt upgrade` para actualizar los paquetes a su versión más reciente.
- Con `sudo apt install apache2 php libapache2-mod-php php-mysql mariadb-server` instalamos todos los paquetes necesarios.

### ⚙️ Configurar MariaDB

- `sudo mysql_secure_installation` inicia la configuración de seguridad de MariaDB para proteger la instalación.

### 📦 Instalar phpMyAdmin

- Se usa `sudo apt install phpmyadmin` para instalar una herramienta web para gestionar bases de datos MySQL/MariaDB.