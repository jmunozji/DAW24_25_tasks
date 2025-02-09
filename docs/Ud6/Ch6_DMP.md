---
title: 'CH6 - DMP'
---

# 📝 UD5 - Despliegue de aplicaciones web - Cheatsheet

> David Maratrat Pons

---

## 🐱 Tomcat

| Comando                                    | Descripción                          |
|--------------------------------------------|--------------------------------------|
| `sudo apt install default-jdk default-jre` | `Instalamos el jdk y el jre de java` |
| `sudo apt install tomcat10 tomcat10-admin` | `Instalamos tomcat`                  |
| `http://IPSERVER:8080`                     | `Accedemos al servidor por la web`   |


### 🙍 Configuración de usuarios

| Configuración                             | Explicación                             |
|-------------------------------------------|-----------------------------------------|
| `sudo nano /etc/tomcat10/tomcat-user.xml` | `Modifica los usuarios en este archivo` |

#### 📄 tomcat-user.xml:

```
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

---

## 🐱 Maven

| Configuración                       | Explicación                          |
|-------------------------------------|--------------------------------------|
| `sudo apt install maven`            | `Instala maven`                      |
| `sudo nano /etc/maven/settings.xml` | `Modifica la configuración de maven` |
| `sudo nano /etc/maven/pom.xml`      | `Modifica los plugins de maven`      |
| `mvn tomcat7:deploy`                | `Despliega la aplicación de maven`   |

#### 📄 settings.xml:

Añade un servidor:
```
<server>
    <id>DesplieguesTomcat</id>
    <username>despliegues</username>
    <password>ieselcaminas</password>
</server>
```

#### 📄 pom.xml:

Plugins para maven:
```
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

---

## ⬢ Node.js

| Configuración                                                                                                                                                                           | Explicación                                                 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| `sudo apt-get install -y ca-certificates curl gnupg`                                                                                                                                    | `Instala las herramientas necesarias para instalar Node.js` |
| `sudo mkdir -p /etc/apt/keyrings`                                                                                                                                                       | `Crea la carpeta de keyrings`                               |
| `curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key  \|  sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg`                                                      | `Obtiene la clave de Node.js`                               |
| `NODE_MAJOR=20 echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" \| sudo tee /etc/apt/sources.list.d/nodesource.list` | `Escoge la versión de Node.js a instalar`                   |
| `sudo apt-get install nodejs -y`                                                                                                                                                        | `Instala Node.js`                                           |

#### 🚀 Express

| Configuración                 | Explicación                                |
|-------------------------------|--------------------------------------------|
| `sudo npm install -g express` | `Instala express de forma global con node` |
| `npm app.js`                  | `Ejecuta una aplicación`                   |

#### 🖥️🖥️🖥️ Clusterización

### 1️⃣ Forma 1
| Configuración         | Explicación                                         |
|-----------------------|-----------------------------------------------------|
| `npm install cluster` | `Instala cluster en el directorio de la aplicación` |

```
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

### 2️⃣ Forma 2

| Configuración             | Explicación                            |
|---------------------------|----------------------------------------|
| `sudo npm install -g pm2` | `Instala pm2 de forma global con node` |
| `pm2 start app.js -i 0`   | `Ejecutamos una app con pm2`           |

---

## 🌍 NETLIFY

| Configuración                     | Explicación                                             |
|-----------------------------------|---------------------------------------------------------|
| `sudo npm install -g netlify-cli` | `Instala el cli de netlify de manera global`            |
| `netlify login`                   | `Iniciamos sesión en netlify`                           |
| `netlify deploy`                  | `Despliega la aplicación en netlify`                    |
| `netlify deploy --prod`           | `Despliega la aplicación en netlify en modo producción` |

---

## 🐉 Python & Flask & Nginx

| Configuración                      | Explicación                                      |
|------------------------------------|--------------------------------------------------|
| `sudo apt-get install python3-pip` | `Instala python`                                 |
| `sudo apt-get install pipenv`      | `Instala pipenv para manejar entornos virtuales` |
| `sudo apt-get install python3-pip` | `Instala el cli de netlify de manera global`     |
| `pipenv shell`                     | `Inicia el entorno virtual`                      |
| `pipenv install flask gunicorn`    | `Instala flask y gunicorn`                       |
| `flask run --host '0.0.0.0'`       | `Inicia el servidor web de flask`                |
| `systemctl enable flask_app`       | `Habilitamos el servicio de la aplicación`       |
| `systemctl start flask_app`        | `Iniciamos el servicio de la aplicación`         |

#### .env

```
FLASK_APP=wsgi.py
FLASK_ENV=production
```

#### flask_app.service

Creamos el servicio para la aplicación
```
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
#### practica_flask

Modificamos el fichero de configuración de nginx para la aplicación
```
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