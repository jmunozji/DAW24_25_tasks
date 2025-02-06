---
title: 'CH6 - RRL'
---

# SERVIDORES DE APLICACIONES

## TOMCAT

### INSTALACIÓN TOMCAT

Instalamos primero el JDK y JRE:

```bash
sudo apt install default-jdk
    
sudo apt-get install default-jre
```

Instalamos Tomcat y su módulo de administración:

```bash
sudo apt-get install -y tomcat10 tomcat10-admin
```

Reiniciamos el servicio de **Tomcat** después de instalarlo:

```bash
sudo apt-get install -y tomcat10 tomcat10-admin
```

### CREACIÓN DE USUARIO EN TOMCAT

Para crear un usuario modificamos el archivo `/etc/tomcat10/tomcat-users.xml`

```bash
sudo nano /etc/tomcat10/tomcat-users.xml
```

Ejemplo de archivo de configuración:

```xml
<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
<user username="despliegues" password="ieselcaminas" roles="manager-script"/>
```

Con la configuración anterior podemos accerder a **Tomcar manager** a través de `http://IP_SERVIDOR:8080/manager` y ya disponemos de un usuario para realizar los deploys con **Maven**.

### DESPLIEGUE DE APLICACIONES

#### PREPARATIVOS PARA SU DESPLIEGUE

Asignamos una contraseña al usuario **Tomcat**:

```bash
sudo passwd tomcat
```

Entramos con ese usuario:

```bash
su tomcat
```

Creamos la estructura de directorios para nuestra aplicación:

```bash
mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes
```

Archivo de ejemplo `hola1.java`:

```Java
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

#### COMPILACIÓN DE LA APLICACIÓN

```bash
javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java
```

#### MOVER EL ARCHIVO COMPILADO

```bash
mv hola1.class ./WEB-INF/classes
```

#### CONFIGURAR EL SERVIDOR DE APLICACIONES

Primero creamos el archivo `web.xml` en el directorio **WEB-INF**

Ejemplo de la configuración:

```xml
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

Ahora podemos acceder a nuestra aplicación a trevés de `http://IPSERVIDOR:8080/prueba/hola1`

### DESPLIEGUE DE APLICACIONES CON MAVEN

#### INSTALACIÓN DE MAVEN

Para realizar la instalación usamos el siguente comando:

```bash
sudo apt install maven
```

Para verificar la versión usamos el siguiente comando:

```bash
mvn --version
```

Para reiniciar el servicio tras las configuraciones usamos el siguiente comando:

```bash
sudo systemctl restart tomcat10.service
```

#### CONFIGURACIÓN DE MAVEN

Primero agregamos un usuario para realizar los deploys en el archivo `/etc/tomcat10/tomcat-users.xml`:

```xml
<tomcat-users>
    <role rolename="manager-script"/>
    <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
</tomcat-users>
```

Ahora editamos el archivo `/etc/maven/settings.xml`

```xml
<server>
  <id>DesplieguesTomcat</id>
  <username>despliegues</username>
  <password>ieselcaminas</password>
</server>
```

#### DESPLIEGUE CON MAVEN

Primero creamos una aplicación con **Maven**:

```bash
mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

Ahora configuramos el archivo `pom.xml`

```xml
<build>
  <finalName>miapp</finalName>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>3.4.0</version>
    </plugin>
    <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
        <url>http://localhost:8080/manager/text</url>
        <server>DesplieguesTomcat</server>
        <path>/miapp</path>
      </configuration>
    </plugin>
  </plugins>
</build>
```

Para desplegar la aplicación usamos el siguiente comando:

```bash
mvn tomcat7:deploy
```

#### COMANDOS ÚTILES

Otros comandos de utilidad son los siguientes:

* Para empaquetar la aplicación en un archivo war -> `mvn package`
* Para volver a desplegar una aplicación tras un cambio -> `mvn tomcat7:redeploy`
* Eliminar la aplicación -> `mvn tomcat7:undeploy`

## NODE EXPRESS

### NODE

#### INSTALACIÓN DE NODE

Descargamos e importamos la clave GPG de Nodesource:

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

Creamos el repositorio deb:

```bash
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

Ejecutamos la actualización e instalación:

```bash
sudo apt-get update
sudo apt-get install nodejs -y
```

Comprobamos que Node.js está correctamente instalado:

```bash
node --version
npm --version
```

#### NODE CON PM2

Primero instalamos **PM2** globalmente:

```bash
sudo npm install pm2 -g
```

Para ejecutar la aplicación en modo clúster con **PM2** usamos el siguiente comando:

```bash
pm2 start app.js -i 0
```

Para detener la aplicación usamos el siguiente comando:

```bash
pm2 stop app.js
```

Para reiniciar la aplicación usamos el siguiente comando:

```bash
pm2 restart app.js
```

Para recargar la aplicación usamos el siguiente comando:

```bash
pm2 reload app.js
```

Para eliminar la aplicación usamos el siguiente comando:

```bash
pm2 delete app.js
```

Para listar las aplicaciones en ejecución usamos el siguiente comando:

```bash
pm2 ls
```

Para mostrar los **logs** de la aplicación usamos el siguiente comando:

```bash
pm2 logs
```

Para mostrar las métricas de rendimiento usamos el siguiente comando:

```bash
pm2 monit
```

##### PM2 CON ECOSYSTEM

Para crear el archivo **Ecosystem** usamos el siguiente comando:

```bash
pm2 ecosystem
```

Ejemplo del archivo generado `ecosystem.config.js`:

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

Podemos usar los comandos de **PM2** para gestionar nuestro archivo `ecosystem.config.js`:

```bash
pm2 [start|restart|reload|stop|delete] ecosystem.config.js
```

### EXPRESS

#### INSTALACIÓN DE EXPRESS

Para instalar **EXPRESS** usamos el siguiente comando:

```bash
sudo npm install -g express
```

Para actualizar **NPM** a una versión más actual usamos el siguiente comando:

```bash
sudo npm install -g npm@10.1.0
```

#### CREAR UN Y DESPLIEGUE DEL PROYECTO

Primero inicilizamos el proyecto **NPM**:

```bash
npm init -y
```

Luego instalamos **EXPRESS** en el proyecto:

```bash
npm install express
```

Para iniciar el servidor usamos el siguiente comando:

```bash
node app.js
```

Por defecto lo mapea en el puerto 3000, podemos acceder al servidor a través de `http://IP_PUBLICA:3000`.

## NETLIFY

### INSTALACIÓN

Para instalar **netlify** globalmente usamos el siguiente comando:

```bash
sudo npm install netlify-cli -g
```

### AUNTENTIFICACIÓN

Para autentificarnos en **netlify** usamos el siguiente comando:

```bash
netlify login
```

### DESPLIEGUE DE PROYECTOS

Para desplegar nuestro proyecto en **netlify** usamos el siguiente comando:

```bash
netlify deploy
```

Para desplegarlo en producción usamos el siguiente comando:

```bash
netlify deploy --prod
```
