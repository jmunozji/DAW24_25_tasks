---
title: 'CH6 - DGD'
---

# CHEATSHEET UNIDAD 6 DESPLIEGUE DE APLICACIONES WEB -  DARIUS GABRIEL DOBRE

## 🙀 TOMCAT

- Primero necesitaremos instalar **java**

```bash
sudo apt install default-jdk
sudo apt install default-jre
```

- Instalación de Tomcat

```bash
sudo apt install tomcat10 tomcat10-admin
```

- Comprobar acceso al servidor:

Tomcat se ejecuta en el puerto 8080

```http
http://IP_SERVIDOR:8080
```
### 📋🙍 FICHERO DE USUARIOS TOMCAT

El fichero para manejar los usuarios se encuentra en: **/etc/tomcat10/tomcat-user.xml**

Se añaden usuarios y roles con la siguiente estructura:

```xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```
### ⬇️ INSTALACIÓN DE MAVEN

```bash
sudo apt install maven
```

#### 💻 AÑADIR SERVER A MAVEN

Tenemos que añadir una configuración en **/etc/maven/settings.xml** para indicarle el servidor que queremos desplegar:

```xml
<server>
    <id>DesplieguesTomcat</id>
    <username>despliegues</username>
    <password>ieselcaminas</password>
</server>
```

### PLUGINS PARA DESPLEGAR EN MAVEN

En el archivo **pom.xml** de nuestro proyecto maven añadimos los siguientes plugins:

```xml
<plugins> 
    <plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <url>http://localhost:8080/manager/text</url> #
        <server>DesplieguesTomcat</server> #
        <path>/rps</path> #
    </configuration>
    </plugin>
</plugins>
```

Para desplegar usamos:

```bash
mvn tomcat7:deploy
```

### 📁📋 ARCHIVOS Y CARPETAS

|ARCHIVO / CARPETA|Descripción|
|-------|-----------|
|/var/lib/tomcat10/webapps|Lugar donde tomcat guarda las aplicaciones que desplegamos|
|webapps/app/WEB-INF/web.xml|Contiene elementos de seguridad, e indica donde se encuentra el servlet para mapearlo|
|/etc/maven/settings.xml| Esta toda la configuración de maven, podemos añadirle un identificador para el servidor que despleguemos|


## 🟩 NODE

### INSTALACIÓN DE NODE

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

```bash
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sour
ces.list.d/nodesource.list
```

NODE_MAJOR es la version de node que queremos

```bash
sudo apt-get update
sudo apt-get install nodejs -y
```

####  INSTALACIÓN DE EXPRESS

Pra instalar express de forma global:

```bash
sudo npm install -g express
```

Para ejecutar una aplicacion en node:

```bash
node app.js
```

### CLUSTERIZAR UNA APLICACIÓN

Para clusterizar una aplicacion podemos hacerlo de varias formas:

- Con js:

```bash
npm install cluster
```

```js
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
```

- Con PM2:

Instalamos pm2:

```bash
sudo npm install pm2 -g
```

Ejecutamos app con pm2:

```bash
pm2 start nombre_aplicacion_sin_cluster.js -i 0
```


## ☁ NETLIFY

Para desplegar una aplicación en netlify:

- Instalamos el cli:

```bash
sudo npm install netlify-cli -g
```

- Hacemos Login

```bash
netlify login
```

- Deploy:

```bash
netlify deploy
```

- Deploy a producción:

```bash
netlify deploy --prod
```


## 🐍 PYTHON CON FLASK

Instalamos python:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install python3-pip
```

Pipenv para gestionar entornos virtuales
```bash
sudo apt-get install pipenv
```

Creamos .env

```js
FLASK_APP=wsgi.py
FLASK_ENV=production
```

### 🖥️ Iniciar entorno virtual

```bash
pienv shell
```

Instalacion flask y gunicorn

```bash
pipenv install flask gunicorn
```

Servidor web integrado de flask:

```bash
flask run --host '0.0.0.0' 
```

### 🚀 SERVIR APP CON NGINX

#### 🍳 CREAR SERVICIO EN SYSTEMD

Creamos un servicio en **/etc/systemd/system/flask_app.service**

```conf
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

Habilitamos e iniciamos servicio:

```bash
systemctl enable flask_app
systemctl start flask_app
```

Configurar Nginx:

```conf
server {
    listen 80;
    server_name practica_flask www.practica_flask;

    access_log /var/log/nginx/practica_flask.access.log;
    error_log /var/log/nginx/practica_flask.error.log;

    location / { 
            include proxy_params;
            proxy_pass http://unix:/var/www/practica_flask/flask_app.sock;
    }
}
```