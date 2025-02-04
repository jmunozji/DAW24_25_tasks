---
title: 'CH6 - PPE'
---

# **📄 Cheatsheet UD6 - Servidores de aplicaciones**

> Este documento resume los comandos y ficheros de configuración para la creación y gestión en diferentes servidores de aplicaciones como **Tomcat** en sistemas Ubuntu, correspondientes a la **Unidad 6** del curso de **Despliegue de Aplicaciones**.


## 🚀 Servidor TOMCAT

### 🛠 Instalación de Tomcat

| **🔧 Comando**                                        | **📝 Funcionalidad**                                                                  |
|-------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo apt-get install default-jdk`                    | Instalar Java Development Kit (JDK).                                                  |
| `sudo apt-get install -y tomcat10 tomcat10-admin`     | Instalar Tomcat 10 y su módulo de administración.                                     |
| `sudo systemctl restart tomcat10`                     | Reiniciar el servicio de Tomcat tras la instalación.                                  |

#### Configuración de Usuarios en Tomcat

📌 Ejemplo de configuración en tomcat-users.xml
```xml
<tomcat-users>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-status"/>
    <role rolename="admin-gui"/>
    <user username="admin" password="ieselcaminas" roles="manager-gui,manager-script,manager-status,admin-gui"/>
</tomcat-users>
```
✅ Una vez configurado, accede a Tomcat Manager desde:

🔗 `http://IP_SERVIDOR:8080/manager`

### 📦 Despliegue de Aplicaciones Java en Tomcat

#### 🏗 Preparación del entorno

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo passwd tomcat`                                                  | Asignar contraseña al usuario tomcat.                                                 |
| `su tomcat`                                                           | Iniciar sesión como usuario tomcat para poder trabajar con sus ficheros.              |
| `mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes`           | Crear estructura de directorios para la aplicación nueva.                             |

#### ✍ Ejemplo de Servlet - hola1.java

```java
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import java.io.*;
public class hola1 extends HttpServlet {
    public void init(ServletConfig conf) throws ServletException {
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

##### 🛠 Compilar el servlet:

```bash
javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java
```
⚠️ Si hay errores, revisa la versión de Java y actualiza javax a jakarta en Java 11+

##### 🔀 Mover el archivo compilado:

```bash
mv hola1.class ./WEB-INF/classes
```

#### ⚙ Configuración del servlet en web.xml

📄 Archivo: WEB-INF/web.xml

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
🎯 Acceso al servlet desde el navegador:

🔗 `http://IP_SERVIDOR:8080/prueba/hola1`


### ⚡ Despliegue de Aplicaciones con Maven

#### 🔧 Instalación de Maven

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo apt-get install maven`                                          | Instalar Apache Maven en el sistema.                                                  |
| `mvn --version`                                                       | Verificar la versión instalada de Maven.                                              |
| `sudo systemctl restart tomcat10.service`                             | Reiniciar Tomcat tras cambiar las configuraciones.                                    |

#### 🔑 Configuración de Maven para despliegue en Tomcat

##### 📄 Editar /etc/maven/settings.xml

```xml
<server>
  <id>DesplieguesTomcat</id>
  <username>despliegues</username>
  <password>ieselcaminas</password>
</server>
```

##### 📄 Agregar usuario en tomcat-users.xml

```xml
<tomcat-users>
    <!-- ... -->
    <role rolename="manager-script"/>
    <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
</tomcat-users>
```

#### 🚀 Despliegue con Maven desde cero

##### 📌 1. Crear una nueva aplicación Maven

```bash
mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

##### 📌 2. Configurar pom.xml para Tomcat

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

##### 📌 3. Comandos para gestionar el despliegue

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `mvn package`                                                         | Empaquetar la aplicación en un archivo WAR.                                           |
| `mvn tomcat7:deploy`                                                  | Desplegar la aplicación en Tomcat.                                                    |
| `mvn tomcat7:redeploy`                                                | Volver a desplegar la aplicación tras cambios.                                        |
| `mvn tomcat7:undeploy`                                                | Eliminar la aplicación del servidor.                                                  |




## 🚀 Servidor Node EXPRESS

### 🛠 Instalación de paquetes

#### 📌 1. Instalar Node.js

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo apt-get install -y ca-certificates curl gnupg`                  | Instala dependencias necesarias.                                                      |
| `sudo mkdir -p /etc/apt/keyrings`                                     | Crea el directorio para las claves GPG.                                               |
| `curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key \| sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg`                                                                    | Importa la clave GPG de NodeSource para verificar la autenticidad del repositorio.    |
| `NODE_MAJOR=20`                                                       | Define la versión de Node.js.                                                         |
| `echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" \| sudo tee /etc/apt/sources.list.d/nodesource.list`                                                                   | Agrega el repositorio de Node.js a la lista de fuentes de APT para su instalación.    |
| `sudo apt-get update`                                                 | Actualiza la lista de paquetes tras añadir el repositorio.                            |
| `sudo apt-get install -y nodejs npm`                                  | Instala Node.js y npm.                                                                |

#### 📌 2. Instalar Express.js

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo npm install -g express`                                         | Instala Express.js de forma global.                                                   |
| `sudo npm install -g npm@latest`                                      | Actualiza npm a la última versión.                                                    |

### 🚀 Ejecución de un Proyecto Node.js con Express

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `npm init -y`                                                         | Inicializa un proyecto Node.js.                                                       |
| `npm install express`                                                 | Instala Express.js en el proyecto.                                                    |
| `node app.js`                                                         | Inicia el servidor en el puerto por defecto (3000).                                   |

🌐 Accede en el navegador:

🔗 `http://IP_PUBLICA:3000`

### ⚡ Ejecución de un Proyecto Node.js con PM2

| **🔧 Comando**                                                        | **📝 Funcionalidad**                                                                  |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo npm install pm2 -g`                                             | Instala PM2 globalmente.                                                              |
| `pm2 start app.js -i 0`                                               | Inicia la aplicación en modo clúster con PM2.                                         |
| `pm2 stop pruebacluster.js`                                           | Detiene la aplicación.                                                                |
| `pm2 ls`                                                              | Lista las aplicaciones en ejecución.                                                  |
| `pm2 logs`                                                            | Muestra los logs en tiempo real.                                                      |
| `pm2 monit`                                                           | Muestra métricas de rendimiento.                                                      |

#### 📄 Configuración con ecosystem.config.js

##### 📌 1. Generar y Modificar el Archivo  

| 🔧 Comando | 📝 Funcionalidad |
|------------|----------------|
| `pm2 init simple` | Crea un archivo `ecosystem.config.js`. |

📄 **Modifica `ecosystem.config.js`**  

```js
module.exports = {
  apps: [
    {
      name: "app",
      script: "app.js",
      instances: 0,  // Usará todas las CPU disponibles
      exec_mode: "cluster",
    },
  ],
};
```

##### 📌 2. Ejecutar con PM2 y Ecosystem  

| 🔧 Comando                                                            | 📝 Funcionalidad                                                                      |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `pm2 start ecosystem.config.js`                                       | Inicia la aplicación con configuración PM2.                                           |
| `pm2 restart ecosystem.config.js`                                     | Reinicia la aplicación.                                                               |
| `pm2 reload ecosystem.config.js`                                      | Recarga sin tiempo de inactividad.                                                    |
| `pm2 stop ecosystem.config.js`                                        | Detiene la aplicación.                                                                |
| `pm2 delete ecosystem.config.js`                                      | Elimina la aplicación.                                                                |




## 🚀 Servidor Netlify (PaaS)

### 🛠 Despliegue con Netlify CLI

> Debemos estar registrados en [Netlify](https://www.netlify.com/) para poder continuar.

| **🔧 Comando**                                        | **📝 Funcionalidad**                                                                  |
|-------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo npm install netlify-cli -g`                     | Instala Netlify CLI globalmente.                                                      |
| `netlify login`                                       | Inicia sesión en Netlify. (Si SSH no permite navegador, usar token de acceso).        |
| `export NETLIFY_AUTH_TOKEN=[TOKEN]`                   | Exportamos el token para poder acceder por SSH.                                       |
| `netlify deploy --prod`                               | Despliega la aplicación en producción.                                                |

📌 Accede a tu aplicación en:

🔗 `https://APP.netlify.app`

> **💡 Tip**: Una vez lo tenemos configurado, podemos vincular nuestro repositorio de [GitHub](https://www.github.com/) y de esta forma cada `git push` generará un nuevo despliegue en Netlify automáticamente.

### ⚡ Despliegue de Aplicaciones Angular

| **🔧 Comando**                                        | **📝 Funcionalidad**                                                                  |
|-------------------------------------------------------|---------------------------------------------------------------------------------------|
| `sudo npm install -g @angular/cli`                    | Instala Angular CLI globalmente.                                                      |
| `ng serve -o --host=IPPRIVADA`                        | Inicia el servidor en la IP privada del equipo.                                       |

📌 Accede en el navegador con la IP pública:

🔗 `http://IPPUBLICA:4200`