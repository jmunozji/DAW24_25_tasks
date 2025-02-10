---

title: 'CH6 - VATC'

---

# 🌟 Servidores de aplicaciones

> ✍️ **Victor Alexandru Teleanu Covaci**

##  **1. Despliegue de aplicaciones Java**

###  **🐱 TOMCAT**

- Instalar Java:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install default-jdk
sudo apt-get install default-jre
```

- Comprobar versión:

```bash
java --version
```

- Instalar Tomcat:

```bash
sudo apt-get install -y tomcat10 tomcat10-admin
```

- Poniendo en el navegador `http://IP_SERVIDOR:8080` comprobamos que tenemos acceso al servidor.

### **🔑 CREACIÓN USUARIO DE TOMCAT**

- Modificamos `sudo nano /etc/tomcat10/tomcat-users.xml`:

```xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

- Para reiniciar Tomcat se utilizará `sudo systemctl restart tomcat10`.

### **⚙️ TOMCAT CON MAVEN**

- Instalar Maven:

```bash
sudo apt-get update
sudo apt-get install maven
```

- Comprobar versión:

```bash
mvn --v
```

- Modificar el fichero `/etc/tomcat10/tomcat-users.xml`:

```xml
<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
<user username="despliegues" password="ieselcaminas" roles="manager-script"/>
```

- Inidicar a Maven el servidor donde vamos a desplegar:

```xml
<server>
    <id>DesplieguesTomcat</id>
    <username>despliegues</username>
    <password>ieselcaminas</password>
</server>
```

#### **📦 PLUGIN PARA TOMCAT**

- Se modifica el archivo `pom.xml`:

```xml
<build>
    <finalName>roshambo</finalName> #
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
</build>
```

- Con `mvn tomcat7:deploy` desplegamos nuestro proyecto.

#### **📂 Despliegue de un proyecto nuevo (ejemplo)**

- Vamos al directorio deseado y ejecutamos:

```bash
mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

## **2. Despliegue de aplicaciones Javascript**

### **🟢 INSTALACIÓN DE NODE**

- Instalar Node.js:

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

- Crear repositorio deb:

```bash
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

- Comprobar versión:

```bash
node --version
npm --version
```

### **🚀 INSTALACIÓN DE EXPRESS**

- Instalar express (global):

```bash
sudo npm install -g express
```

- Ejecutar el proyecto:

```bash
node app.js
```

### **🔄 APLICACIÓN CLUSTERIZADA**

#### 📌 Ejemplo de aplicación con Cluster:

```js
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
    const numCPUs = os.cpus().length;
    console.log(`Master ${process.pid} is running`);
    for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }

    cluster.on('exit', (worker, code, signal) => {
        console.log(`Worker ${worker.process.pid} died`);
    });
} else {
    http.createServer((req, res) => {
        res.writeHead(200);
        res.end('Hello World!');
    }).listen(8000);

    console.log(`Worker ${process.pid} started`);
}
```

#### 🛠️ Usando PM2:

- Instalar PM2:

```bash
sudo npm install pm2 -g
```

- Ejecutar aplicación:

```bash
pm2 start nombre_aplicacion_sin_cluster.js -i 0
```

- Podemos iniciar, reiniciar, recargar, detener y eliminar una aplicación con:

```bash
$ pm2 start nombre_aplicacion
$ pm2 restart nombre_aplicacion
$ pm2 reload nombre_aplicacion
$ pm2 stop nombre_aplicacion
$ pm2 delete nombre_aplicacion
```

## **3. NETLIFY (PaaS)**

| **🔧 Comando**                        | **📝 Descripción**          |
|----------------------------------------|-----------------------------|
| `sudo npm install netlify-cli -g`      | Instalar el CLI de Netlify  |
| `netlify login`                        | Loguearse a Netlify         |
| `netlify deploy`                       | Despliega proyecto          |
| `netlify deploy --prod`                | Despliega a producción      |

## **4. Despliegue de aplicaciones Python**

- Instalar Python:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install python3-pip
```

- Instalar el paquete pipenv para gestionar entornos virtuales:

```bash
sudo apt-get install pipenv
```

- Comprobar versión:

```bash
pipenv --version
```

- Crear .env:

```bash
cd /var/www/practica_flask
touch .env
```

```bash
FLASK_APP=wsgi.py
FLASK_ENV=production
```

- Iniciar entorno virtual:

```bash
cd /var/www/practica_flask
pipenv shell
```

- Instalar dependencias necesarias:

```bash
pipenv install flask gunicorn
```

### **⚙️ Crear archivo para que systemd corra Gunicorn**

- Crear y modificar `/etc/systemd/system/flask_app.service`:

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

- Habilitamos el servicio:

```bash
systemctl enable flask_app

systemctl start flask_app
```

- Modificar `/etc/nginx/sites-available/practica_flask`:

```bash
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