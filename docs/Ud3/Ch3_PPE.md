---
title: 'CH3 - PPE'
---

# **Cheatsheet UD3 - Servidores Web**

> Este documento resume los comandos y ficheros de configuración para la creación y gestión de servidores Web en **Nginx** y **Apache2** en sistemas Ubuntu, correspondientes a la **Unidad 3** del curso de **Despliegue de Aplicaciones**.




## 🖥️ Instalación de Servidores paso a paso (Linux)

La instalación puede variar dependiendo de nuestra distribución de Linux. A continuación, se presenta solo la instalación en sistemas **Ubuntu**.

### 🌐 Instalación del servidor Nginx

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt update && sudo apt upgrade`              | Actualiza la lista y los paquetes instalados a la última versión disponible.                                 |
| `sudo apt install nginx`                           | Instala los paquetes para configurar el servicio Nginx.                                                      |
| `systemctl status nginx`                           | Muestra el estado del servicio Nginx.                                                                        |
| `sudo nano /var/www/html/index.html`               | Comando para modificar la plantilla principal que muestra el servicio Nginx                                  |
| `less /var/log/nginx/access.log`                   | Muestra el contenido del archivo de registro con cada **solicitud** registrada en el servidor Nginx.         |
| `tail -n 3 /var/log/nginx/error.log`               | Muestra las tres ultimas lineas del archivo de registro con cada **error** registrado en el servidor Nginx.  |

### 🌐 Instalación del servidor Apache2

Para instalar apache2, debemos seguir los mismos pasos que en la instalación de Nginx pero sustituyendo los comandos de `nginx` por `apache2`. A continuación, se muestran los cambios en la siguiente tabla:

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt install apache2`                         | Instala los paquetes para configurar el servicio Apache2.                                                    |
| `systemctl status apache2`                         | Muestra el estado del servicio Apache2.                                                                      |
| `less /var/log/apache2/access.log`                 | Muestra el contenido del archivo de registro con cada **solicitud** registrada en el servidor Apache2.       |
| `tail -n 3 /var/log/apache2/error.log`             | Muestra las tres ultimas lineas del archivo de registro con cada **error** registrado en el servidor Apache2.|

> **💡 Tip**: Para acceder al servidor con nuestro navegador y comprobar que esta funcionando correctamente, simplemente debemos introducir la IP pública de nuestro servidor en el navegador utilizando un protocolo HTTP `http://IP_SERVIDOR`. Debemos asegurarnos que el estado de nuestro servicio sea <span style="color: green;">**active (running)**</span>




## 🖥️ Creación de Sitios Virtuales

### 👤 Preparación de Usuarios Gestores

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo usermod -g www-data <usuario>`               | Ponemos al usuario que vayamos a utilizar en el grupo con permisos para gestionar las plantillas.            |
| `sudo mkdir -p /var/www/sitio1`                    | Creamos una carpeta para almacenar las plantillas de nuestro sitio virtual.                                  |
| `sudo chown <usuario>:www-data /var/www/sitio1`    | Asignamos al usuario y el grupo `www-data` como propietario del directorio.                                  |
| `sudo chmod 755 /var/www/sitio1`                   | Le damos permisos a usuarios y grupos para que el servidor web pueda leer el contenido y navegar dentro de los directorios.                                                                                                                                                        |

> **💡 Tip**: Dentro del directorio `sitio1`, podremos crear plantillas para dar forma a nuestro sitio virtual. Para que funcione correctamente, es recomendable crear al menos un fichero `index.html` con una plantilla básica para que el servidor tenga una respuesta a las peticiones de los clientes que acceden al sitio virtual.

### 🌐 Modificar la configuración en Nginx

> En el directorio `/etc/nginx` se guardan los ficheros de configuración. Para crear un sitio virtual, debemos crear un archivo dentro de la carpeta `/etc/nginx/sites-avaliable` con un aspecto similar al siguiente:

```nginx
server {
        # Puerto en el que el servidor escuchará las solicitudes HTTP
        listen 80;
        listen [::]:80;

        # Directorio raíz del sitio web
        root /var/www/sitio1;

        # Archivos de índice que se buscarán en el directorio raíz
        index index.html index.htm index.nginx-debian.html;

        # Nombre de dominio
        server_name sitio1;

        location / {
                # Intenta servir el archivo solicitado, si no se encuentra, devuelve un error 404
                try_files $uri $uri/ =404;
        }
}
```

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/` | Creamos un enlace simbólico para que se habilite el sitio virtual.                     |
| `sudo systemctl restart nginx`                     | Reiniciamos el servicio nginx para que se apliquen los cambios realizados.                                   |

### 🌐 Modificar la configuración en Apache2

> En apache2, debemos configurar el archivo de configuración en la ruta `/etc/apache2/sites-avaliable` y asegurarnos que el fichero tenga la extensión `.conf` para que funcione correctamente. Un ejemplo del archivo para apache sería:

```apache
<VirtualHost *:80>
        # Nombre de dominio
        ServerName sitio1

        # Directorio raíz del sitio web
        DocumentRoot /var/www/sitio1

        <Directory /var/www/sitio1>
                # Permite la sobrescritura de configuraciones en este directorio
                AllowOverride All

                # Permite el acceso a todos los usuarios
                Require all granted
        </Directory>
</VirtualHost>
```
| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo a2ensite sitio1`                             | Habilitamos el sitio virtual que hemos creado en el fichero de configuracion.                                |
| `sudo systemctl restart apache2`                   | Reiniciamos el servicio apache para que se apliquen los cambios realizados.                                  |

### ✅ Comprobación del funcionamiento

Si no tenemos un servidor DNS, debemos configurar manualmente nuestro cliente para que pueda acceder al sitio virtual de dos formas:

- Si tenemos acceso a un usuario administrador (sudo), podemos modificar el archivo `/etc/hosts` y añadir la siguiente linea: `IP_SERVIDOR  sitio1`.
- Si no tenemos acceso al usuario administrador, podemos lanzar nuestra navegador desde terminal con un parámetro que indique el servidor DNS que quieres que aplique con el siguiente comando: `google-chrome --host-resolver-rules="MAP sitio1 IPSERVER" --ignore-certificate-error`.




## 🖥️ Autenticación

> Antes de empezar, debemos instalar la herramienta `htpasswd` para generar contraseñas con el comando `sudo apt install apache2-utils`

### 🌐 Preparación del entorno (Nginx)

Debemos modificar el fichero de configuración del sitio virtual para que, en la ruta que nosotros queramos, habilitemos las restricciones de acceso.

```nginx
    location / {
            auth_basic  "Área restringida";
            auth_basic_user_file    /etc/nginx/.htpasswd;
            try_files $uri $uri/ =404;
        }
```

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo htpasswd -c /etc/nginx/.htpasswd <usuario>`  | Creamos el archivo oculto que almacema los usuario y contraseñas que tienen acceso, y añadimos un primer usuario (para añadir otro usuario, podemos ejecutar el mismo comando sin la opción `-c`).                                                                                         |

### 👤 Restricciones por IP

También podemos realizar la restricción de acceso por dirección IP y combinarla de forma efectiva con la autenticación HTTP de la siguiente forma:

```nginx
    location / {
            satisfy all;
            deny 192.168.1.2;
            allow 192.168.1.3;
            deny all;

            auth_basic  "Área restringida";
            auth_basic_user_file    /etc/nginx/.htpasswd;
        }
```
### 🌐 Preparación del entorno (Apache2)

Para habilitar las autenticación en un sitio virtual apache2, hay que modificar el fichero `/etc/apache2/sites-available/sitio1.conf` y añadir el siguiente contenido:

```apache
<Directory /var/www/sitio1>
        AuthType Basic
        AuthName "Intranet"
        AuthBasicProvider file
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
</Directory>
```

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo htpasswd -c /etc/apache2/.htpasswd <usuario>`| De la misma forma que en Nginx, debemos crear el archivo que almacena los usuarios y un usuario inicial.     |
| `ls -la /etc/apache2/mods-enabled/`                | Comprobamos si los modulos de autenticación de apache2 están habilitados.                                    |
| `a2enmod auth_basic`                               | Habilitamos los módulos si hay alguno que no aparece.                                                        |

> **💡 Tip**: Es importante que, al terminar las configuraciones, reiniciemos el servicio (tanto en apache2 como en nginx) para que los cambios se apliquen correctamente antes de hacer las comprobaciones.




## 🖥️ Activar el protocolo HTTPS

### 🌐 Configuración en Nginx

- Podemos crear el certificado y las claves de forma simultánea con un único comando: `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt`

Y una vez los tenemos generados, debemos modificar el fichero de configuración `/etc/nginx/sites-available` eliminando y añadiendo las siguientes lineas:

```diff
-        listen 80;
-        listen [::]:80;
+        listen 443 ssl;
+        ssl_certificate /etc/nginx/ssl/server.crt;
+        ssl_certificate_key     /etc/nginx/ssl/server.key;
+        ssl_ciphers 'TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384';
+        ssl_protocols TLSv1.2 TLSv1.3;
+        access_log /var/log/nginx/https_access.log;
```

Por último, para **forzar la redirección a HTTPS**, debemos añadir en el fichero de configuración otro bloque “server” adicional y separado del otro con una sintaxis similar a esta:

```nginx
server {
        listen 80;
        server_name sitio1;

        # Ruta del archivo de registro de accesos
        access_log /var/log/nginx/http_access.log;

        # Redirige todas las solicitudes HTTP a HTTPS
        return 301 https://sitio1$request_uri;
}
```

### 🌐 Configuración en Apache2

> Los certificados y las claves se crean con el mismo comando que en Nginx, pero cambiando la ruta a `/etc/apache2/ssl`, y debemos asegurarnos de que el módulo `ssl` esté activo.

Y luego modificar el fichero de configuración para que quede similar a este:

```apache
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

De la misma forma que en Nginx, para habilitar la **redirección forzosa a HTTPS** debemos añadir un nuevo bloque “VirtualHost” con la siguiente sintaxis:

```apache
<VirtualHost *:80>
    ServerName sitio1
    Redirect permanent / https://sitio1/
</VirtualHost>
```




## 🖥️ Instalación de la pila LAMP

Vamos a hacer una plataforma web LAMP completa, formada por un servidor web (apache2), un gestor de bases de datos (mariadb) y un lenguaje interpretado PHP.

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo systemctl status apache2`                    | Comprobamos que tenemos un servidor web instalado y corriendo en nuestro equipo.                             |
| `sudo apt install mariadb-server`                  | Instalamos el servidor de bases de datos.                                                                    |
| `sudo mysql_secure_installation`                   | Ejecutamos el script para securizar el servicio de bases de datos (importante configurar una contraseña para el usuario administrador).                                                                                                                                             |
| `sudo apt-get install php libapache2-mod-php php-mysql` | Instalamos los paquetes de php necesarios para gestionar los servicios anteriores.                      |
| `sudo systemctl restart apache2`                   | Reiniciamos el servicio web para aplicar los paquetes que hemos instalado.                                   |

- Con ello, podemos comprobar si funciona correctamente creando un fichero básico **PHP** en el directorio `/var/www/html` y accediendo al servidor web desde nuestro navegador buscando la ruta `http://IPservidor/NOMBRE_FICHERO.php`.