---
title: 'CH3 - AEM'
---

# **1. Instalación de Nginx en EC2 Debian AWS** ### **Instalar Nginx**

```bash

sudo apt update && sudo apt upgrade -y

sudo apt install nginx

``` ### **Comprobar Nginx**

```bash

systemctl status nginx

```

### **Modificar página de bienvenida**

1. Edita el archivo de muestra:

```bash

sudo nano /var/www/html/index.nginx-debian.html

```

## **2. Instalación de Apache en EC2 Debian AWS**

### **Crear EC2**

- Nombre: `servidorApache`

- Usa el mismo Grupo de seguridad: `ServidorWeb`.

### **Instalar Apache**

```bash

sudo apt update && sudo apt upgrade -y

sudo apt install apache2

```

### **Verificar funcionamiento**

1. Comprueba el servicio:

```bash

systemctl status apache2

```

2. Modifica la página por defecto en su directorio correspondiente.

---

## **3. Nginx en Docker**

### **Dockerfile para Nginx**

```Dockerfile

FROM debian:latest

MAINTAINER Alex Musat <musateduardalexandru@gmail.com>

RUN apt update && apt upgrade -y && apt install -y nginx && \

rm -rf /var/lib/apt/lists/*

EXPOSE 80 CMD ["nginx", "-g", "daemon off;"] ```

### **Comandos Docker** 1. **Construir la imagen:** ```bash docker build -t servernginx . ``` 2. **Crear y ejecutar el contenedor:**

```bash

docker run --name=miservernginx -d -p 80:80 servernginx

```

3. **Detener y eliminar contenedor:**

```bash

docker stop miservernginx

docker rm miservernginx

```

### **Montar volumen persistente**

1. Crea un directorio local:

```bash

mkdir -p ~/web

```

2. Agrega un archivo `index.html` en `~/web` con el contenido que desees.

3. Ejecuta el contenedor con volumen:

```bash

docker run -d --name=miservernginx \

--mount type=bind,source=/home/tu_usuario/web,target=/var/www/html \

-p 80:80 \

servernginx

```

---

## **4. Nginx usando la imagen oficial de Docker**

1. Usa la imagen oficial:

```bash

docker run -d --name=miservernginx2 \

-v /home/tu_usuario/web:/usr/share/nginx/html:ro \

-p 80:80 \

nginx:latest

```

---

## **5. Dockerizar Apache**

1. **Dockerfile para Apache:**

```Dockerfile

FROM debian:latest

RUN apt update && apt install -y apache2

CMD ["apache2ctl", "-D", "FOREGROUND"]

EXPOSE 80

```

2. **Construir y ejecutar contenedor:**

```bash

docker build -t serverapache .

docker run -d --name=miserverapache -p 80:80 serverapache

```

3. **Montar directorio local:**

```bash

docker run -d --name=miserverapache \

--mount type=bind,source=/home/tu_usuario/web,target=/var/www/html \

-p 80:80 \

serverapache

```

4. **Usar la imagen oficial de Apache:**

```bash

docker run -d --name=miserverapache2 \

-v /home/tu_usuario/web:/usr/local/apache2/htdocs:ro \

-p 80:80 \

httpd:latest

```

### **Configuración de Nginx**

#### **1. Cambiar propietario y permisos**

```bash

sudo chown -R www-data:www-data /var/www/html

sudo chmod 644 /var/www/html/index.html

sudo chmod 755 /var/www/html

```

---

#### **2. Crear un sitio virtual**

1. Crear usuario y grupo:

```bash

sudo useradd -m usuario1

sudo usermod -g www-data usuario1

sudo passwd usuario1

```

2. Crear espacio para el sitio:

```bash

sudo mkdir -p /var/www/sitio1

sudo chown usuario1:www-data /var/www/sitio1

sudo chmod 755 /var/www/sitio1

```

3. Crear contenido de la página:

```bash

su usuario1

nano /var/www/sitio1/index.html

```

4. Archivo de configuración de Nginx:

```bash

sudo nano /etc/nginx/sites-available/sitio1

```

**Contenido del archivo**:

```nginx

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

5. Crear enlace simbólico y reiniciar:

```bash

sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/

sudo systemctl restart nginx

```

---

#### **3. Probar el sitio**

- Editar `/etc/hosts` (Linux) o `C:\Windows\System32\drivers\etc\hosts` (Windows):

```

IP_PUBLICA_SERVIDOR sitio1

```

- Acceder a `http://sitio1` desde el navegador.

---

### **Configuración de Apache2**

#### **1. Crear un sitio virtual**

1. Crear archivo de configuración:

```bash

sudo nano /etc/apache2/sites-available/sitio1.conf

```

**Contenido del archivo**:

```

<VirtualHost *:80>

ServerName sitio1

DocumentRoot /var/www/sitio1

<Directory /var/www/sitio1>

AllowOverride All

Require all granted

</Directory>

</VirtualHost>

```

2. Habilitar el sitio:

```bash

sudo a2ensite sitio1

sudo systemctl restart apache2

```

---

### **Configuración en Docker**

#### **1. Estructura de directorios**

```plaintext

~

├── sitiosvirtuales/

│ ├── html/index.html

│ └── sitio1/index.html

└── nginxvirtual/

├── Dockerfile

├── docker-compose.yaml

├── entrypoint.sh

└── sites-available/

├── default

└── sitio1

```

#### **2. Archivos necesarios**

- **Dockerfile**:

```dockerfile

FROM debian:latest

RUN apt update && apt install -y nginx

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]

```

- **docker-compose.yaml**:

```yaml

services:

nginx:

build: .

volumes:

- ../sitiosvirtuales:/var/www

- ./sites-available:/etc/nginx/sites-available

ports:

- 80:80

```

- **entrypoint. sh**:

```bash

#!/bin/bash

rm -f /etc/nginx/sites-enabled/*

for file in /etc/nginx/sites-available/*; do

ln -s "$file" /etc/nginx/sites-enabled/ || true

done

nginx -g "daemon off;"

```

#### **3. Levantar el servicio**

```bash

docker-compose up -d

```

#### **4. Probar y detener**

- Detener el servicio:

```bash

docker-compose down

```

## **Autenticación en Nginx**

### **1. Preparación del entorno**

1. **Requisitos previos**:

- Completa y verifica la práctica de sitios virtuales en Nginx.

2. **Crea la estructura**:

```bash

su usuario1

mkdir /var/www/sitio1/intranet

cp /var/www/sitio1/index.html /var/www/sitio1/intranet

nano /var/www/sitio1/intranet/index.html

```

3. **Asegúrate de poder acceder**:

- Modifica `/etc/hosts` o usa:

```bash

google-chrome --host-resolver-rules="MAP sitio1 IPSERVER" --ignore-certificate-error

```

---

### **2. Creación del archivo de contraseñas**

1. **Instalar herramienta `htpasswd`**:

```bash

sudo apt install apache2-utils

```

2. **Crear archivo `.htpasswd`**:

```bash

sudo htpasswd -c /etc/nginx/.htpasswd profe

```

- Introduce "profe" como contraseña.

3. **Añadir más usuarios**:

```bash

sudo htpasswd /etc/nginx/.htpasswd <usuario>

```

- Crea usuarios con tu nombre y apellido.

4. **Verifica**:

```bash

cat /etc/nginx/.htpasswd

```

---

### **3. Configuración de autenticación básica**

1. **Editar configuración de `sitio1`**:

```bash

sudo nano /etc/nginx/sites-available/sitio1

```

- Añadir en el bloque `location /`:

```nginx

auth_basic "Área restringida";

auth_basic_user_file /etc/nginx/.htpasswd;

```

2. **Reiniciar Nginx**:

```bash

sudo systemctl restart nginx

```

---

### **4. Autenticación parcial**

1. **Aplica autenticación solo a `/intranet`**:

- Modifica el archivo de configuración:

```nginx

location /intranet {

auth_basic "Área restringida";

auth_basic_user_file /etc/nginx/.htpasswd;

}

```

---

### **5. Restricción por IP**

1. **Bloquear acceso a IP específica**:

```nginx

location /intranet {

allow 192.168.1.1/24;

deny 192.168.1.2;

deny all;

auth_basic "Área restringida";

auth_basic_user_file /etc/nginx/.htpasswd;

}

```

2. **Combinar restricciones con `satisfy`**:

- Requiere autenticación **y** IP válida:

```nginx

satisfy all;

```

- Requiere autenticación **o** IP válida:

```nginx

satisfy any;

```

---

### **6. Verificación**

- Realiza pruebas en ventanas privadas y revisa logs:

```bash

tail -n 10 /var/log/nginx/access.log

tail -n 10 /var/log/nginx/error.log

```

---

## **Autenticación en Apache2**

### **1. Creación del archivo de contraseñas**

1. **Crear archivo `.htpasswd`**:

```bash

sudo htpasswd -c /etc/apache2/.htpasswd profe

```

---

### **2. Configuración de autenticación básica**

1. **Editar configuración del sitio**:

```bash

sudo nano /etc/apache2/sites-available/sitio1.conf

```

- Añade el bloque:

```apache

<Directory /var/www/sitio1/intranet>

AuthType Basic

AuthName "Intranet"

AuthBasicProvider file

AuthUserFile /etc/apache2/.htpasswd

Require valid-user

</Directory>

```

2. **Reiniciar Apache**:

```bash

sudo systemctl restart apache2

```

---

### **3. Activación de módulos en Apache2**

1. **Verificar módulos disponibles y activados**:

```bash

ls -la /etc/apache2/mods-available/

ls -la /etc/apache2/mods-enabled/

```

2. **Activar módulos requeridos**:

```bash

a2enmod auth_basic

a2enmod authn_file

a2enmod authz_user

sudo systemctl restart apache2

```

#### **1. Configuración HTTPS en Nginx**

##### **A. Creación de Certificado Autofirmado**

1. Crear directorio para certificados:

```bash

sudo mkdir /etc/nginx/ssl

```

2. Generar certificado autofirmado:

```bash

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt

```

3. Verifica la creación:

```bash

ls -l /etc/nginx/ssl

```

---

##### **B. Configuración de HTTPS en el sitio virtual**

1. Editar el archivo de configuración:

```bash

sudo nano /etc/nginx/sites-available/sitio1

```

2. Modificar el bloque `server`:

```nginx

server {

listen 443 ssl;

ssl_certificate /etc/nginx/ssl/server.crt;

ssl_certificate_key /etc/nginx/ssl/server.key;

ssl_protocols TLSv1.2 TLSv1.3;

ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';

access_log /var/log/nginx/https_access.log;

}

```

3. Reiniciar Nginx:

```bash

sudo systemctl restart nginx

```

---

##### **C. Redirección Forzada de HTTP a HTTPS**

1. Añadir un nuevo bloque `server`:

```nginx

server {

listen 80;

server_name sitio1;

access_log /var/log/nginx/http_access.log;

return 301 https://sitio1$request_uri;

}

```

2. Eliminar cualquier referencia a `listen 80` del bloque `server` principal.

3. Reiniciar Nginx:

```bash

sudo systemctl restart nginx

```

---

##### **D. Comprobaciones**

1. Accede a:

```

https://sitio1

```

Añade una excepción al navegador si aparece una advertencia de certificado.

2. Verifica la redirección:

```

http://sitio1

```

Deberías ser redirigido automáticamente a HTTPS.

3. Logs:

- `http_access.log`: Peticiones redirigidas.

- `https_access.log`: Peticiones HTTPS exitosas.

---

#### **2. Configuración HTTPS en Apache2**

##### **A. Activar módulo SSL**

1. Comprobar si está activo:

```

ls -la /etc/apache2/mods-enabled/ssl*

```

2. Activar si no lo está:

```

sudo a2enmod ssl

sudo systemctl restart apache2

```

---

##### **B. Generar Certificado Autofirmado**

1. Crear directorio para certificados:

```

sudo mkdir /etc/apache2/ssl

```

2. Generar certificado:

```

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/server.key -out /etc/apache2/ssl/server.crt

```

---

##### **C. Configuración HTTPS en el sitio virtual**

1. Editar el archivo del sitio virtual:

```

sudo nano /etc/apache2/sites-available/sitio1.conf

```

2. Añadir el bloque:

```

<VirtualHost *:443>

ServerName sitio1

DocumentRoot /var/www/sitio1

SSLEngine on

SSLCertificateFile /etc/apache2/ssl/server.crt

SSLCertificateKeyFile /etc/apache2/ssl/server.key

<Directory /var/www/sitio1>

AllowOverride All

Require all granted

</Directory>

</VirtualHost>

```

3. Reiniciar Apache:

```

sudo systemctl restart apache2

```

---

##### **D. Redirección Forzada de HTTP a HTTPS**

1. Añadir bloque adicional: ``` <VirtualHost *:80> ServerName sitio1 Redirect permanent / https://sitio1/ </VirtualHost> ``` 2. Reiniciar Apache: ```bash sudo systemctl restart apache2 ``` ---

##### **E. Comprobaciones**

1. Accede a: ``` https://sitio1 ``` Añade una excepción al navegador si es necesario. --- ### **Errores Comunes**

1. **Error: `ERR_SSL_PROTOCOL_ERROR` en Nginx**

- Verifica que el bloque `server` para HTTPS incluye: ```nginx listen 443 ssl; ssl_certificate /etc/nginx/ssl/server.crt; ssl_certificate_key /etc/nginx/ssl/server.key; ``` - Confirma que los certificados existen y son accesibles:

```bash ls -l /etc/nginx/ssl ```

2. **Apache responde en HTTP por defecto** - Asegúrate de que el sitio virtual HTTPS está habilitado: ```bash sudo a2ensite sitio1 sudo systemctl restart apache2 ``` --- ## **Cheat Sheet: Instalación de la Pila LAMP en Debian**

### **Instalación de Apache**

1. **Instalar Apache**:

```bash sudo apt install apache2 ```

2. **Verificar que Apache está funcionando**:

```bash sudo systemctl status apache2 ```

3. **Si no está activo, habilitar y arrancar**:

```bash sudo systemctl enable apache2 sudo systemctl start apache2 ```

4. **Probar Apache**:

- Abre un navegador y accede a `http://IP_Pública`. ---

### **Instalación de MariaDB (Gestor de Bases de Datos)**

1. **Instalar MariaDB**:

```bash sudo apt install mariadb-server ```

2. **Verificar que MariaDB está funcionando**:

```bash sudo systemctl status mysql ```

3. **Configurar la instalación de MariaDB**: ```bash sudo mysql_secure_installation ``` - Cambiar la contraseña del root a `ieselcaminas`.

4. **Acceder a MariaDB**:

```bash mysql -u root -p ```

- Ingresar la contraseña cuando se te pida.

---

### **Instalación de PHP**

1. **Instalar PHP y los paquetes necesarios**:

```bash sudo apt-get install php libapache2-mod-php php-mysql ```

2. **Reiniciar Apache para que reconozca PHP**:

```bash sudo systemctl restart apache2 ```

3. **Crear archivo de prueba PHP**:

```bash sudo nano /var/www/html/info.php ```

- Añadir el siguiente código:

```php <?php phpinfo(); ?> ```

4. **Probar PHP**:

- Acceder a `http://IP_Pública/info.php` desde tu navegador.

---

### **Instalación de phpMyAdmin (Interfaz Gráfica de Base de Datos)**

1. **Instalar phpMyAdmin**:

```bash

sudo apt-get install phpmyadmin

```

2. **Configurar phpMyAdmin**:

- Seleccionar `apache2` como el servidor web.

- Configurar con `dbconfig-common` y usar la contraseña `ieselcaminas` para root.

3. **Acceder a phpMyAdmin**:

- Abrir el navegador y acceder a `http://IP_Pública/phpmyadmin`.

- Iniciar sesión con `root` y la contraseña `ieselcaminas`.

4. **Si phpMyAdmin no es accesible**, crear un enlace simbólico:

```bash

sudo ln -s /usr/share/phpmyadmin/ /var/www/html/phpmyadmin

```
