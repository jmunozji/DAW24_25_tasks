---
title: 'CH3 - RPE'
---

# 🌐 **Cheatsheet de Servidores Web y Pila LAMP**

## **NGINX** 🟢

### 🛠 **Instalación y Configuración Básica**

| **Descripción**                         | **Comando**                              |
|-----------------------------------|------------------------------------|
| Actualizar repositorios y sistema | `sudo apt update && sudo apt upgrade` |
| Instalar Nginx                    | `sudo apt install nginx`           |
| Comprobar el estado de Nginx      | `sudo systemctl status nginx`      |

### 📁 **Directorios Importantes**

| **Ubicación**                     | **Descripción**                                    |
|-------------------------------------|-------------------------------------------------------|
| `/etc/nginx/nginx.conf`             | Archivo principal de configuración de Nginx.          |
| `/etc/nginx/sites-available/`       | Configuración de sitios disponibles.                 |
| `/etc/nginx/sites-enabled/`         | Configuración de sitios habilitados (enlaces simbólicos a sites-available). |
| `/etc/nginx/conf.d/`                | Archivos de configuración adicionales.               |
| `/var/www/html/`                    | Directorio por defecto para archivos web estáticos.  |
| `/var/log/nginx/access.log`         | Archivo de registro de accesos.                      |
| `/var/log/nginx/error.log`          | Archivo de registro de errores.                      |

### 🌍 **Configuración de Sitios Virtuales**

1. **Crear directorio para el sitio:**
   ```bash
   sudo mkdir -p /var/www/sitiovirtual
   sudo chown usuario1:www-data /var/www/sitiovirtual
   sudo chmod 755 /var/www/sitiovirtual
   ```

2. **Configurar archivo de sitio:**

   ```bash
   sudo nano /etc/nginx/sites-available/sitiovirtual
   ```
   **Contenido básico:**

   ```nginx
   server {
       listen 80;
       root /var/www/sitiovirtual;
       index index.html;
       server_name sitiovirtual;
       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

3. **Habilitar el sitio:**

   ```bash
   sudo ln -s /etc/nginx/sites-available/sitiovirtual /etc/nginx/sites-enabled/
   ```

4. **Reiniciar Nginx:**

   ```bash
   sudo systemctl restart nginx
   ```

### 🔑 **Autenticación en Nginx**

1. **Crear fichero de contraseñas**:

    ```bash
    sudo apt install apache2-utils
    htpasswd -c /etc/nginx/.htpasswd usuario1
    ```

2. **Configurar autenticación básica**:

    ```nginx
    server {
        listen 80;
        root /var/www/sitiovirtual;
        index index.html;
        server_name sitiovirtual;
        location / {
            auth_basic "Área privada";
            auth_basic_user_file /etc/nginx/.htpasswd;
            try_files $uri $uri/ =404;
        }
    }
    ```

    ##### 🔒 **Combinar autenticación con restricciones de IP**:

    ```nginx
    location /private {
        satisfy all;

        allow 192.168.1.0/24;
        deny all;

        auth_basic "Área privada";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
    ```

### 🔒 **Activar HTTPS con Certificados SSL**

1. **Crear certificados auto-firmados:**

   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt
   ```

2. **Configurar archivo para SSL:**

    ```nginx
    server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/server.crt;
        ssl_certificate_key /etc/nginx/ssl/server.key;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;  ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
        ssl_protocols TLSv1.2 TLSv1.3;
        access_log /var/log/nginx/https_access.log;


       root /var/www/sitiovirtual;
       index index.html;
       server_name sitiovirtual;
    }
    ```

3. **Redirigir HTTP a HTTPS:**

   ```nginx
   server {
       listen 80;
       server_name sitiovirtual;
       access_log /var/log/nginx/http_access.log;
       return 301 https://$host$request_uri;
   }
   ```

---

## **APACHE** 🟠

### 🛠 **Instalación y Configuración Básica**

| **Tarea**                         | **Comando**                              |
|-----------------------------------|------------------------------------|
| Actualizar repositorios y sistema | `sudo apt update && sudo apt upgrade` |
| Instalar Apache                   | `sudo apt install apache2`         |
| Comprobar el estado de Apache     | `sudo systemctl status apache2`    |


### 📁 **Directorios Importantes**

| **Ubicación**                       | **Descripción**                                       |
|-------------------------------------|-------------------------------------------------------|
| `/etc/apache2/apache2.conf`          | Archivo principal de configuración de Apache.         |
| `/etc/apache2/sites-available/`     | Configuración de sitios disponibles.                 |
| `/etc/apache2/sites-enabled/`       | Configuración de sitios habilitados (enlaces simbólicos a sites-available). |
| `/etc/apache2/conf-available/`      | Archivos de configuración disponibles.               |
| `/etc/apache2/conf-enabled/`        | Archivos de configuración habilitados (enlaces simbólicos a conf-available). |
| `/var/www/html/`                    | Directorio por defecto para archivos web estáticos.  |
| `/var/log/apache2/access.log`       | Archivo de registro de accesos.                      |
| `/var/log/apache2/error.log`        | Archivo de registro de errores.                      |

### 🌍 **Configuración de Sitios Virtuales**

1. **Crear directorio para el sitio**:

   ```bash
   sudo mkdir -p /var/www/sitiovirtual
   sudo chown usuario1:www-data /var/www/sitiovirtual
   sudo chmod 755 /var/www/sitiovirtual
   ```

2. **Configurar archivo del sitio**:

    ```bash
    sudo nano /etc/apache2/sites-available/sitiovirtual.conf
    ```

    ```apache
    <VirtualHost *:80>
        ServerName sitiovirtual
        DocumentRoot /var/www/sitiovirtual
        <Directory /var/www/sitiovirtual>
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>
    ```

### 🔑 **Autenticación en Apache**

1. **Crear fichero de contraseñas**:

    ```bash
    sudo apt install apache2-utils
    htpasswd -c /etc/nginx/.htpasswd usuario1
    ```

2. **Configurar autenticación**:

    ```apache
    <VirtualHost *:80>
        ServerName sitiovirtual
        DocumentRoot /var/www/sitiovirtual
        <Directory /var/www/sitiovirtual>
                AllowOverride All
                Require all granted
        </Directory>
        <Directory /var/www/sitiovirtual/secret>
                AuthType Basic
                AuthName "Secret area"
                AuthBasicProvider file
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>
    </VirtualHost>
    ```	

3. **Habilitar módulos de autenticación**:

    ```bash
    sudo a2enmod auth_basic authn_file authz_user
    sudo systemctl restart apache2
    ```

4. **Reiniciar Apache**:

    ```bash
    sudo systemctl restart apache2
    ```

### 🔐 **Activación HTTPS en Apache**

1. **Activar el módulo ssl en Apache2**:

    ```bash
    sudo a2enmod ssl
    sudo systemctl restart apache2
    ```

2. **Modificar el archivo de configuración para activar HTTPS**:

    ```apache
    <VirtualHost *:443>
        ServerName sitiovirtual
        DocumentRoot /var/www/sitiovirtual

        SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/server.crt
        SSLCertificateKeyFile /etc/apache2/ssl/server.key

        <Directory /var/www/sitiovirtual>
                AllowOverride All
                Require all granted
        </Directory>
    </VirtualHost>
    ```

3. **Redirección forzosa de HTTP a HTTPS**:

    ```bash
    <VirtualHost *:80>
        ServerName sitiovirtual
        Redirect permanent / https://sitiovirtual/
    </VirtualHost>
    ```

---

# 🚀 **Instalación de la Pila LAMP**

### 1️⃣ **Instalación de Apache**

```bash
sudo apt install apache2
```

##### ✅ **Verificar el estado de Apache**

```bash
sudo systemctl status apache2
```

### 2️⃣ **Instalación de MariaDB**

```bash
sudo apt install mariadb-server
```

##### ✅ **Verificar el estado de MariaDB**

```bash
sudo systemctl status mariadb
```

### 3️⃣ **Instalación de PHP**

```bash
sudo apt-get install php libapache2-mod-php php-mysql
sudo systemctl restart apache2
```

### 4️⃣ **Instalación de phpMyAdmin**

```bash
sudo apt-get install phpmyadmin
sudo ln -s /usr/share/phpmyadmin/ /var/www/html/phpmyadmin
```

##### ✅ **Verificar el estado de phpMyAdmin**

```bash
http://<IP-DEL-SERVIDOR>/phpmyadmin
```
