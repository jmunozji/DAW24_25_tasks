---
title: 'CH6 - RPE'
---

# **📄 Cheatsheet UD6 - Servidores de aplicaciones**

---

## 🏗️ Tomcat

### 🛠 Instalación de Tomcat

| **🔧 Comando**                                        | **📝 Descripción**                                                                  |  
|-------------------------------------------------------|-------------------------------------------------------------------------------------|  
| `sudo apt-get install default-jdk`                    | Instala el kit necesario para desarrollar aplicaciones en Java. |  
| `sudo apt-get install default-jre`                    | Descarga e instala el entorno requerido para ejecutar programas en Java. |  
| `sudo apt-get install -y tomcat10 tomcat10-admin`     | Instala el servidor Tomcat 10 junto con su panel de administración. |  
| `sudo systemctl restart tomcat10`                     | Vuelve a iniciar el servicio de Tomcat para aplicar los cambios. |
| `sudo systemctl status tomcat10`                      | Comprueba el estado del servicio de Tomcat. |

#### 🔑 Configuración de Usuarios en Tomcat

Edita el archivo de configuración de usuarios de Tomcat:

```bash
sudo nano /etc/tomcat10/tomcat-users.xml
```

Inserta el siguiente contenido:

```xml
    <role rolename="manager-status"/>
    <role rolename="admin-gui"/>
    <user username="admin" password="ieselcaminas" roles="manager-gui,manager-script,manager-status,admin-gui"/>
```

Una vez terminado, reinicia el servicio de Tomcat:

```bash
sudo systemctl restart tomcat10
```

Y ya podras acceder a la interfaz de administración de Tomcat en `http://{IP-del-servidor}:8080/manager`

#### 🚀 Despliegue de aplicaciones con Tomcat

#### 👤 Usuario Tomcat

Aquí tienes la información en formato de tabla:  

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |  
|------------------------------|--------------------------------------------------------------|
| `sudo passwd tomcat`         | Asigna una nueva contraseña al usuario `tomcat`.            |
| `su tomcat`                  | Cambia al usuario `tomcat10` usando la contraseña creada.   |

Si necesitas más cambios, dime.

#### 📂 Creación de una aplicación nueva

Crar la estructura de las carpetas:

```bash
mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes
```

Ejemplo de aplicación:

Crear hola1.java:

```java
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

Compilar la aplicación:

```bash
javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java
```

Moveremos la aplicacion a la carpeta de aplicaciones:

```bash
mv hola1.class ./WEB-INF/classes
```

#### ⚙️ Configuración del servidor de aplicaciones

Crear el archivo web.xml en la carpeta WEB-INF/:

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

Ya se podrá acceder a la aplicación en `http://{IP-del-servidor}:8080/prueba/hola1`

### 🚀 Despliegue de aplicaciones con Maven

#### 🛠 Instalación de Maven

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |  
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|  
| `sudo apt-get install maven`                                          | Descargar e instalar Apache Maven en el equipo.                                      |  
| `mvn --version`                                                       | Comprobar la versión actual de Maven.                                               |  
| `sudo systemctl restart tomcat10.service`                             | Volver a iniciar Tomcat después de modificar su configuración.                      |  

#### ⚙️ Configuración de Maven

##### 📝 Modificar archivo `/etc/tomcat10/tomcat-users.xml`

```xml
     <role rolename="admin-gui"/>
     <role rolename="manager-gui"/>
     <role rolename="manager-script"/>
     <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
     <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
```

##### 📝 Editar el archivo de configuración `/etc/maven/settings.xml`

```xml
    <server>
      <id>DesplieguesTomcat</id>
      <username>despliegues</username>
      <password>ieselcaminas</password>
    </server>
```

#### 📦 Despliegue de un proyecto con Maven

##### 🏗️ Crear una aplicación con Maven

```bash
    mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

##### ⚙️ Configurar `pom.xml`

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

##### 🚀 Desplegar la aplicación

| **🔧 Comando**          | **📝 Descripción**                                               |  
|-------------------------|----------------------------------------------------------------|  
| `mvn tomcat7:deploy`   | Publica la aplicación en el servidor Tomcat.                  |  
| `mvn tomcat7:redeploy` | Realiza un nuevo despliegue tras aplicar modificaciones.      |  
| `mvn tomcat7:undeploy` | Retira la aplicación del entorno de ejecución.               |  

---

## 🖥️ Node Express

### 🛠 Instalación de Node Express

#### 🛠 Instalación de Node

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo apt-get install -y ca-certificates curl gnupg`                  | Descarga e instala los paquetes esenciales para el proceso.                          |
| `sudo mkdir -p /etc/apt/keyrings`                                     | Genera el directorio necesario para almacenar claves GPG.                            |
| `curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key \| sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg`                                                                    | Obtiene y convierte la clave GPG de NodeSource para validar la fuente del repositorio. |
| `NODE_MAJOR=20`                                                       | Especifica la versión principal de Node.js a utilizar.                               |
| `echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" \| sudo tee /etc/apt/sources.list.d/nodesource.list`                                                                   | Integra el repositorio oficial de Node.js en el sistema de gestión de paquetes.       |
| `sudo apt-get update`                                                 | Recarga la base de datos de paquetes para reflejar los cambios recientes.            |
| `sudo apt-get install -y nodejs npm`                                  | Descarga e instala Node.js junto con su gestor de paquetes npm.                      |

#### 🚀 Instalación de Express

| **🔧 Comando**                                                        | **📝 Descripción**                                                                   |  
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|  
| `sudo npm install -g express`                                         | Realiza una instalación global de Express.js.                                         |  
| `sudo npm install -g npm@latest`                                      | Descarga e instala la versión más reciente de npm a nivel global.                     |  

### 🏗️ Creación de un proyecto

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `mkdir proyecto`                                                         | Crea una carpeta para el nuevo proyecto.               |
| `npm init -y`                                                         | Crea un nuevo proyecto de Node.js con una configuración predeterminada.               |
| `npm install express`                                                 | Agrega Express.js como dependencia en el proyecto.                                   |
| `node app.js`                                                         | Ejecuta el servidor, que por defecto escucha en el puerto 3000.                      |

Ya puedes acceder a la aplicación en `http://{IP-del-servidor}:3000`

### 🚀 Despliegue de una aplicación clusterizada con PM2

#### 🛠 Instalación de PM2

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo npm install -g pm2`                                             | Descarga e instala PM2 en el sistema.                                               |

#### 🎛️ Control de una aplicación con PM2

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |  
|-----------------------------------------------------------------------|------------------------------------------------------------------------------------|  
| `pm2 start app.js -i 0`                                               | Ejecuta la aplicación en múltiples instancias con PM2.                             |  
| `pm2 stop app.js`                                           | Finaliza la ejecución de la aplicación.                                            |  
| `pm2 ls`                                                              | Muestra un listado de las aplicaciones activas.                                    |  
| `pm2 logs`                                                            | Visualiza los registros en tiempo real.                                            |  
| `pm2 monit`                                                           | Presenta estadísticas de uso y rendimiento.                                        |  

#### ⚙️ Configuración de una aplicación clusterizada con PM2

Crear un archivo de configuración de PM2:

```bash
touch ecosystem.config.js
```

Insertar el siguiente contenido:

```js
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

#### 🚀 Despliegue de una aplicación clusterizada con PM2

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |  
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `pm2 start ecosystem.config.js`                                       | Ejecuta la aplicación utilizando la configuración de PM2.                            |
| `pm2 restart ecosystem.config.js`                                     | Vuelve a iniciar la aplicación.                                                      |
| `pm2 reload ecosystem.config.js`                                      | Recarga la aplicación sin causar interrupciones.                                     |
| `pm2 stop ecosystem.config.js`                                        | Finaliza la ejecución de la aplicación.                                              |
| `pm2 delete ecosystem.config.js`                                      | Borra la aplicación del administrador de procesos.                                   |

---

## ☁️ Netlify (PaaS)

### 🛠 Instalación de Netlify

| **🔧 Comando**                                                        | **📝 Descripción**                                                                  |  
|-------------------------------------------------------|----------------------------------------------------------------------------------------|
| `sudo npm install netlify-cli -g`                     | Descarga e instala Netlify CLI a nivel global.                                        |
| `netlify login`                                       | Autentica el usuario en Netlify. (Si el navegador no abre por SSH, usar un token).   |
| `export NETLIFY_AUTH_TOKEN=[TOKEN]`                   | Configura el token de autenticación para acceso vía SSH.                              |
| `netlify deploy --prod`                               | Publica la aplicación en el entorno de producción.                                   |

Y ya podremos acceder a la aplicación en `https://{nombre-de-la-aplicacion}.netlify.app`