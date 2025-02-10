---
title: 'CH6 - BSO'
---

# **T6: Servidores de aplicaciones**

## Apache Tomcat para desplegar Aplicaciones Java
![Imagen Apache Tomcat](https://itdconsulting.com/wp-content/uploads/2022/04/banner-pestana-apache-tomcat.jpg)

### Instalación de Tomcat

`sudo apt-get update && apt-get upgrade`:  Actualizar antes
`java --version`: Comprobar si java está instalado

Si no está instalado java, lo instalaremos usando:

```bash
sudo apt-get install default-jre
sudo apt-get install default-jre
```

Instalar Tomcat

```bash
sudo apt-get install -y tomcat10 tomcat10-admin #Instalación de Tomcat
sudo systemctl status tomcat10 #Comprobamos si está instalado
```

Tips para habilitar tomcat o cualquier servicio

```bash
sudo systemctl start tomcat10 #Iniciamos tomcat
sudo systemctl enable tomcat10 #Conseguimos que el servicio se inicie
                               #siempre al encender el PC
```

Comprobar acceso a tomcat desde un navegador

`http://IP_SERVIDOR:8080`: Acceder desde una navegador

![Imagen tomcat navegador](https://jmunozji.github.io/DAW/Ud6%20Servidores%20de%20aplicaciones/P3_1/03.png)

### Configuración de Tomcat

#### Gestión de Usuarios y roles

```bash
sudo nano /etc/tomcat10/tomcat-users.xml #Modificamos este fichero
```

- "Gestor de Aplicaciones Web de Tomcat" deberemos activar el rol "manager-gui".
- "Gestor de Máquina Virtual de Tomcat" necesitaremos activar el rol "admin-gui".

```xml
  <!--Añadimos estas líneas antes del cierre-->
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

Si reiniciamos tomcat con `sudo systemctl restart tomcat10`, e ingresamos a `http://IP_SERVIDOR:8080/manager`, podemos acceder al manager, donde nos prodremos logear.

![Imagen del manager de Tomcat](https://jmunozji.github.io/DAW/Ud6%20Servidores%20de%20aplicaciones/P3_1/04.png)

### Despliegue de Aplicaciones Web de Tomcat

#### Mediante un archivo .war

1. Descargamos el archivo .war
1. Dentro del manager, buscamos donde poder seleccionar el archivo y desplegar el .war
1. Acceder a la aplicación mediante `http://IP_SERVIDOR:8080/aplicacion`

#### Mediante Maven

1. `sudo apt-get install maven`: Instalación de maven
1. `mvn --v`: Comprobar si está correctamente instalada
1. Indicar a Maven el servidor que vamos a desplegar(Tomcat)
    1. `/etc/tomcat10/tomcat-users.xml`
        ```xml
        <role rolename="admin-gui"/>
        <role rolename="manager-gui"/>
        <role rolename="manager-script"/>
        <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
        <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
        ```
    1. `sudo systemctl restart tomcat10.service`: Reiniciar tomcat
    1. `/etc/maven/settings.xml`
        ```xml
        <server>
            <id>DesplieguesTomcat</id>
            <username>despliegues</username>
            <password>ieselcaminas</password>
        </server>
        ```
1. `pom.xml`: Este archivo está en el proyecto que queremos desplegar
    ```diff
    <build>
        <finalName>roshambo</finalName> #
    +    <plugins> 
    +        <plugin>
    +            <groupId>org.apache.tomcat.maven</groupId>
    +            <artifactId>tomcat7-maven-plugin</artifactId>
    +            <version>2.2</version>
    +            <configuration>
    +                <url>http://localhost:8080/manager/text</url> #
    +                <server>DesplieguesTomcat</server> #
    +                <path>/rps</path> #
    +            </configuration>
    +        </plugin>
        </plugins>
    </build>
    ```
1. `mvn tomcat7:deploy`: Desplegar proyecto

## Node.js y Express para desplegar aplicaciones JavasCript

![Imagen Node con express](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Finternet-israel.com%2Fwp-content%2Fuploads%2F2016%2F04%2Fexpress.jpg&f=1&nofb=1&ipt=43d5483fbf6c897147a4c97f3ec181f7f5d353d98d50aed9450941728447a179&ipo=images)

### Instalación de Node.js

```bash
#Añadir repositorio de Node en Debian
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

#Indicamos que versión de Node instalar
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sour
ces.list.d/nodesource.list

#Instalación de Node
sudo apt-get update
sudo apt-get install nodejs -y
```

### Instalación de Express

```sh
sudo npm install -g express #Instalación de express de manera global
node app.js #Ejecutar Aplicación con Node
```

### Despliegue de un proyecto con Express

1. Consigues el proyecto que quieres desplegar, ya sea mediante git o uno que ya tengas en tu PC.
1. `cd proyecto`: Entras a la carpeta del proyecto.
1. `npm install`: Instalas las dependencias necesarias.
1. `package.json`: Consultas cómo desplegar aplicación.
    ```json
    "scripts": {
    "start:watch": "nodemon ./bin/www --ignore public/",
    "start:dev": "node ./bin/www",
    "debug": "node debug ./bin/www",
    "start": "NODE_ENV=production node ./bin/www",
    },
    ```
1. `npm run start`: Inicias la aplicación.
1. Acceder a la ruta que te da, `http://IP_PUBLICA:3000`

### Clusterizar una aplicación mediante JS con Node y Express

```bash 
npm install cluster #Instalamos cluster
```

Clusterizamos la aplicación mediante JS con Node y Express

```js
const express = require("express");
const port = 3000;
const cluster = require("cluster");
const totalCPUs = require("os").cpus().length;

if (cluster.isMaster) {
 console.log(`Number of CPUs is ${totalCPUs}`);
 console.log(`Master ${process.pid} is running`);

 // Fork workers.
 for (let i = 0; i < totalCPUs; i++) {
 cluster.fork();
 }

 cluster.on("exit", (worker, code, signal) => {
 console.log(`worker ${worker.process.pid} died`);
 console.log("Let's fork another worker!");
 cluster.fork();
 });
}
```

### Clusterizar una aplicación mediante PM2 sin modificar el programa

```bash
sudo npm install pm2 -g #Instalamos pm2 de forma global
```

Ejecutamos el programa sin clusters programados.

```bash
pm2 start nombre_aplicacion_sin_cluster.js -i 0
```

- `-i`: Indicará a PM2 que inicie la aplicación en cluster_mode.

## Desplegar aplicación mediante Netlify

![Imágen de Netlify](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fwww.hawksworx.com%2Fimages%2Fnetlify-banner.png&f=1&nofb=1&ipt=7595170fc39e20526396b8a663481ed5d7bd19db81ca7f4c609c89d1bebd8f3c&ipo=images)

1. `sudo npm install netlify-cli -g`: Instalar globalmente Netlify.
1. `netlify login`: Iniciar sesión en Netlify.
1. `netlify deploy`: Desplegamos el proyecto, nos preguntará.
    1. ruta: por ejemplo dist o el directorio necesario dependiendo del proyecto.
1. `netlify deploy --prod`: Desplegará el proyecto en producción.

## Desplegar aplicación mediante Python y Flask

![Imágen de python y Flask](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fbizflyportal.mediacdn.vn%2Fthumb_wm%2F1000%2C100%2Fbizflyportal%2Fimages%2Ffla16172661186968.jpg&f=1&nofb=1&ipt=911d28a915a151cc4651f9e156d7f765f1792685ed320573c9390c493055e698&ipo=images)

### Instalación

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install python3-pip #Instalamos python y pip(gestor de paquetes)

#Instalamos pipenv para gestionar los entornos virtuales
sudo apt-get install pipenv
pipenv --version #Versión instalada
```

### Creación y despligue de aplicación Flask

1. Accedemos al proyecto de python
1. `touch .env`: Contendrá las variables de entorno necesarias
    1. Editamos el archivo
    ```.env
    FLASK_APP=wsgi.py
    FLASK_ENV=production
    ```
1. `pipenv shell`: Iniciamos el entorno virtual para cargar las variables de entorno
1. `pipenv install flask gunicorn`: instalamos dependencias
1. `touch application.py wsgi.py`: Creamos archivos para aplicación de Flask
    1. `application.py`
        ```py
        from flask import Flask
        app=Flask(__name__)
        @app.route('/')
        def index():
                '''Index page route'''
                return '<h1>Aplicacion desplegada<h1>'
        ```
    1. `wsgy.py`
        ```py
        from application import app
            if __name__=='__main__':
            app.run(debug=false)
        ```
1. `flask run --host '0.0.0.0'`: Ejecutamos la aplicación
    1. `http://IP-maq-virtual:5000`: Accedemos al servidor

### Desplegar aplicación usando NGINX

#### Instalación de NGINX
```bash
sudo apt-get install nginx #Instalar nginx
```

Iniciar servicio de nginx

```bash
sudo systemctl start nginx
sudo systemctl status nginx
sudo systemctl enable nginx #Ejecutar siempre al iniciar PC
```

#### Crear servicio para la aplicación

Crear archivo `/etc/systemd/system/flask_app.service` para correr Gunicorn

```bash
[Unit]
Description=flask_app.service
After=network.target

[Service]
User=admin
Group=www-data
Environment="PATH=/home/admin/.local/share/virtualenvs/practica_flask-gV07D8Rz/bin:$PATH"
WorkingDirectory=/var/www/practica_flask/
ExecStart=/home/admin/.local/share/virtualenvs/practica_flask-gV07D8Rz/bin/gunicorn --workers 2 --bind unix:/var/www/practica_flask/flask_app.sock -m 007 wsgi:app

[Install]
WantedBy=multi-user.target
```

Iniciar y habilitar servicio

```bash
systemctl enable flask_app
systemctl start flask_app
```

#### Configurar Nginx para correr el servicio

Creamos el archivo `/etc/nginx/sites-available/practica_flask`
```sh
server {
    listen 80;
    server_name practica_flask www.practica_flask; #

    access_log /var/log/nginx/practica_flask.access.log; #
    error_log /var/log/nginx/practica_flask.error.log;

    location / { 
            include proxy_params;
            proxy_pass http://unix:/var/www/practica_flask/flask_app.sock; #
    }
}
```

Creamos enlace simbólico

`sudo ln -s /etc/nginx/sites-available/practica_flask /etc/nginx/sites-enabled/`

Desplegar aplicación mediante Nginx

```bash
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl status nginx
```