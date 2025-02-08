---
title: 'CH6 - EV'
---

## **Servidores de aplicaciones**

### Instalación de Tomcat

- Instalar Java
```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install default-jdk
sudo apt-get install default-jre
```
- Instalar Apache Tomcat
: `sudo apt-get install -y tomcat10 tomcat10-admin`
- Comprobar instalación
: `systemctl status tomcat10`

#### Creación de un usuario

- Editar fichero `tomcat-users.xml`
: `sudo nano /etc/tomcat10/tomcat-users.xml`
- Añadir las siguientes líneas
```bash
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

- Reiniciar tomcat
: `sudo systemctl restart tomcat10`

### Despliegue de aplicaciones con Tomcat

- Desplegar archivo `.war`.

#### Crear una aplicación nueva

- Acceder a `/etc/passwd` y cambiar la línea
`tomcat:x:994:994:Apache Tomcat:/var/lib/tomcat:/usr/sbin/nologin` por
`tomcat:x:994:994:Apache Tomcat:/var/lib/tomcat:/bin/bash`
- Asingarle una constraseña
: `sudo passwd tomcat`
- Entrar en el sistema como usuario `tomcat`
: `su tomcat`
- Crear estructura de carpetas
: `mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes`
- Crear archivo `.java` dentro de `prueba`
```bash
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
public class hola1 extends HttpServlet {
 public void init(ServletConfig conf)
    throws ServletException {
    super.init(conf);
 }

public void service(HttpServletRequest req, HttpServletResponse res)
 throws ServletException, IOException {
    res.setContentType("text/html");
    PrintWriter out = res.getWriter();
    out.println("<html>");
    out.println("<body>");
    out.println("<h1>servlet hola 1</h1>");
    out.println("</body>");
    out.println("</html>");
    }
}
```
- Compilar aplicación
: `javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java`
- Mover archivo `.class` a `webapps/prueba/WEB-INF/classes`
: `mv hola1.class ./WEB-INF/classes`
- En el directorio `WEB-INF` crear fichero `.xml` con el siguiente contenido
```bash
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
    <display-name>Ejemplos tutorial</display-name>
    <description>Servlets de ejemplos del tutorial de Servlets y JDBC.</description>
    <servlet>
        <servlet-name>hola1</servlet-name>
        <servlet-class>hola1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>hola1</servlet-name>
        <url-pattern>/hola1</url-pattern>
    </servlet-mapping>
</web-app>
```
- Fichero `.html` dentro de `prueba`
```bash
<html>
    <head>
        <title>Actividad de prueba con Tomcat</title>
    </head>
    <body bgcolor=white>
        <div style=" width:100% text-align:center">
            <img width="200" src="tomcat.gif">
            <br>
            <h1>Actividad con Tomcat</h1>
            <p>Despliegue de aplicaciones web</p>
        </div>
        <div style="background:#ffc; margin-top:30px; padding:30px; text-align:center">
            Ir a <a href="hola1.jsp">JSP page</a>
            <br>
            Ir al <a href="hola1">servlet</a>
        </div>
    </body>
</html>
```
- Fichero `.jsp` dentro de `prueba`
```bash
<html>
    <head>
        <title>Ejemplo página JSP</title>
    </head>
    <body bgcolor=white>
        <div style=" width:100%; text-align:center">
            <img width="150" src="tomcat.gif">
            <br>
            <h1>Ejemplo JSP hola1</h1>
            <br>
            <%= new String("Hola 1") %>
        </div>
    </body>
</html>
```

### Despliegue en Tomcat con Maven

- Instalar Maven
: `sudo apt-get install maven`
- Añadir usuario en `tomcat-users.xml`
- Reiniciar Tomcat
: `sudo systemctl restart tomcat10.service`
- Editar `settings.xml` y añadir
```bash
    <server>
      <id>DesplieguesTomcat</id>
      <username>despliegues</username>
      <password>ieselcaminas</password>
    </server>
```

#### Despliegue de un proyecto ya preparado

- Clonar el proyecto
```bash
git clone https://github.com/cameronmcnz/rock-paper-scissors.git
cd rock*
git checkout patch-1
```
- Modificar el `POM` del proyecto. Añadir lo siguiente dentro del bloque `<plugins>`
```bash
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
```
- Desplegar una aplicacion
: `mvn tomcat7:deploy`
- Replegar una aplicacion
: `mvn tomcat7:undeploy`
- Volver a desplegar una aplicacion
: `mvn tomcat7:redeploy`

### Despliegue de aplicaciones con Node Express

#### Instalacion Node.js

- Descargar e importar clave GPG de Nodesecure
```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```
- Crear repositorio deb
```bash
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```
- Ejecutar la actualización e instalación
```bash
sudo apt-get update
sudo apt-get install nodejs -y
```

#### Instalación de Express

`sudo npm install -g express`

#### Creación del primer proyecto

- Inicializar proyecto
: `npm init -y`
- Instalar Express para el proyecto de manera local
: `npm install express`
- Crear archivo de muestra `.js` con el siguiente contenido
```bash
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('Hello. Welcome to this blog')
})

app.listen(port, () => {
    console.log(`Example app listening at http://localhost:${port}`)
})
```
- Ejecutar el proyecto
: `node app.js`

### Dockerización del despliegue de una aplicación Node.js

- Clonar repositorio
: `git clone https://github.com/contentful/the-example-app.nodejs.git`
- Hacer build de la imagen Docker
: `docker build -t the-example-app.nodejs .`
- Iniciar el contenedor
: `docker run -p 3000:3000 -d the-example-app.nodejs`

### Despliegue de una aplicación "clusterizada" con Node Express

- Instalar `loadtest`
: `sudo npm install -g loadtest`
- Ejecturar la aplicación que queremos probar
- En otro terminal, ejecutar una prueba de carga
: `loadtest http://localhost:3000/api/500000 -n 1000 -c 100`
- Detener la aplicación sin clústers, ejecutar la que sí que tiene
y realizar las mismas pruebas

#### Uso de PM2 para administrar un clúster de Node.js

- Instalar PM2
: `sudo npm install pm2 -g`
- Usar en la aplicación no clusterizada
: `pm2 start nombre_aplicacion_sin_cluster.js -i 0`
- Ejecutar las mismas pruebas que anteriormente, pero con PM2
- Detener la aplicación
: `pm2 stop app.js`
- Crear archivo `Ecosystem`
: `pm2 ecosystem`
- Contenido archivo `ecosystem.config.js`
```bash
module.exports = {
 apps: [
 {
 name: "nombre_aplicacion",
 script: "nombre_aplicacion_sin_cluster.js",
 instances: 0,
 exec_mode: "cluster",
 },
 ],
};
```
- Ejecutar la aplicación
: `pm2 start ecosystem.config.js`
- Se puede ejecutar, reiniciar, recargar, detener y eliminar una aplicación con los siguientes comandos
```bash
$ pm2 start nombre_aplicacion
$ pm2 restart nombre_aplicacion
$ pm2 reload nombre_aplicacion
$ pm2 stop nombre_aplicacion
$ pm2 delete nombre_aplicacion

# Cuando usemos el archivo Ecosystem:

$ pm2 [start|restart|reload|stop|delete] ecosystem.config.js
```

### Despliegue de una aplicación en Netlify (PaaS)

- Instalar el CLI de Netlify
: `sudo npm install netlify-cli -g`
- Autenticarse
: `netlify login`
- Instalar dependencias indicadas en `package.json`
: `npm install`
- Probar el script `start`
: `npm run start`
- Hacer un pre-deploy
: `netlify deploy`
- Pasar aplicación a producción
: `netlify deploy --prod`

#### Despliegue mediante conexión con GitHub

- Crear un repositorio desde cero en GitHub
- Ejecutar los siguientes comandos
```bash
$ git init
$ git add .
$ git commit -m "Subiendo el código..."
$ git branch -M main
```
- Referenciar la carpeta y hacer un push
```bash
$ git remote add origin https://github.com/username/practicaTresSeis.git
$ git push -u origin main
```
- Importar proyecto de Git en Netlify en modo gráfico y desplegar la aplicación
