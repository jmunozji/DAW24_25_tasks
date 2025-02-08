---
title: 'CH6 - EGM'
---

# **📄 Cheatsheet UD6 - Servidores de aplicaciones**

## 🚀 Servidor TOMCAT

### 🛠 Instalación de Tomcat

**🔧 Instalar y configurar Tomcat**
```sh
sudo apt-get update && sudo apt-get upgrade # Actualiza los paquetes del sistema.
sudo apt-get install default-jdk # Instala Java Development Kit (JDK).
sudo apt-get install default-jre # Instala Java Runtime Environment (JRE).
sudo apt-get install -y tomcat10 tomcat10-admin # Instala Tomcat 10 y su módulo de administración.
sudo systemctl restart tomcat10 # Reinicia el servicio de Tomcat tras la instalación.
systemctl status tomcat10 # Verifica que el servicio de Tomcat está activo.
```

### 🔑 Creación de Usuarios en Tomcat

📌 Modificar el archivo de configuración:
```bash
sudo nano /etc/tomcat10/tomcat-users.xml
```

📄 Ejemplo de configuración:
```xml
<tomcat-users>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-status"/>
    <role rolename="admin-gui"/>
    <user username="admin" password="ieselcaminas" roles="manager-gui,manager-script,manager-status,admin-gui"/>
</tomcat-users>
```
✅ Acceder a Tomcat Manager desde:
🔗 `http://IP_SERVIDOR:8080/manager`

### 📦 Despliegue de Aplicaciones Java en Tomcat

#### 🏗 Preparación del entorno

**🔧 Configuración inicial**
```sh
sudo passwd tomcat # Asigna una contraseña al usuario tomcat.
su tomcat # Inicia sesión como usuario tomcat para trabajar con sus archivos.
mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes # Crea la estructura de directorios para la aplicación nueva.
```

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

**🔧 Compilar el servlet:**
```sh
javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java # Compila el servlet.
    ⚠️ Si hay errores, revisa la versión de Java y actualiza javax a jakarta en Java 11+.
```

**🔧 Mover el archivo compilado:**
```sh
mv hola1.class ./WEB-INF/classes # Mueve el archivo compilado a la carpeta correspondiente.
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

---

### 🌐 Creación de Página HTML de Prueba

📄 Archivo: prueba/index.html

```html
<html>
    <head>
        <title>Actividad de prueba con Tomcat</title>
    </head>
    <body bgcolor=white>
        <div style="width:100% text-align:center">
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

### 🖥 Página JSP de Ejemplo

📄 Archivo: prueba/hola1.jsp

```html
<html>
    <head>
        <title>Ejemplo página JSP</title>
    </head>
    <body bgcolor=white>
        <div style="width:100%; text-align:center">
            <img width="150" src="tomcat.gif">
            <br>
            <h1>Ejemplo JSP hola1</h1>
            <br>
            <%= new String("Hola 1") %>
        </div>
    </body>
</html>
```

📌 Ahora puedes desplegar y probar tu aplicación en Tomcat 🎯

---

## ⚡ Despliegue de Aplicaciones con Maven

### 🔧 Instalación de Maven

```sh
sudo apt-get install maven # Instalar Apache Maven en el sistema.
mvn --version # Verificar la versión instalada de Maven.
sudo systemctl restart tomcat10.service # Reiniciar Tomcat tras cambiar las configuraciones.
```

### 🔑 Configuración de Maven para despliegue en Tomcat

#### 📄 Editar `/etc/maven/settings.xml`

```xml
<server>
  <id>DesplieguesTomcat</id>
  <username>despliegues</username>
  <password>ieselcaminas</password>
</server>
```

#### 📄 Agregar usuario en `tomcat-users.xml`

```xml
<tomcat-users>
    <role rolename="manager-script"/>
    <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
</tomcat-users>
```

### 🚀 Despliegue con Maven desde cero

#### 📌 1. Crear una nueva aplicación Maven

```bash
mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false # Generar una aplicación web Maven.
```

#### 📌 2. Configurar `pom.xml` para Tomcat

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

#### 📌 3. Comandos para gestionar el despliegue

```sh
mvn package # Empaquetar la aplicación en un archivo WAR.
mvn tomcat7:deploy # Desplegar la aplicación en Tomcat.
mvn tomcat7:redeploy # Volver a desplegar la aplicación tras cambios.
mvn tomcat7:undeploy # Eliminar la aplicación del servidor.
```

### 🔄 Despliegue de un Proyecto Existente

#### 📌 1. Clonar el Proyecto desde GitHub

```bash
git clone https://github.com/cameronmcnz/rock-paper-scissors.git # Clonar el repositorio desde GitHub.
cd rock-paper-scissors # Moverse al directorio del proyecto.
git checkout patch-1 # Cambiar a la rama específica.
```

#### 📌 2. Modificar `pom.xml`

Agregar lo siguiente dentro del bloque `<plugins>`:

```xml
<plugin>
  <groupId>org.apache.tomcat.maven</groupId>
  <artifactId>tomcat7-maven-plugin</artifactId>
  <version>2.2</version>
  <configuration>
    <url>http://localhost:8080/manager/text</url>
    <server>DesplieguesTomcat</server>
    <path>/rps</path>
  </configuration>
</plugin>
```

🎯 Ahora puedes desplegar y administrar aplicaciones Java en Tomcat usando Maven de manera eficiente.

---

## 🚀 Servidor Node EXPRESS

### 🛠 Instalación de paquetes

#### 📌 1. Instalar Node.js

```sh
sudo apt-get install -y ca-certificates curl gnupg # Instala dependencias necesarias.
sudo mkdir -p /etc/apt/keyrings # Crea el directorio para las claves GPG.
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg # Importa la clave GPG de NodeSource.
NODE_MAJOR=20 # Define la versión de Node.js.
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list # Agrega el repositorio de Node.js.
sudo apt-get update # Actualiza la lista de paquetes.
sudo apt-get install -y nodejs npm # Instala Node.js y npm.
```

#### 📌 2. Instalar Express.js

```sh
sudo npm install -g express # Instala Express.js de forma global.
sudo npm install -g npm@latest # Actualiza npm a la última versión.
```

### 🚀 Ejecución de un Proyecto Node.js con Express

```sh
npm init -y # Inicializa un proyecto Node.js.
npm install express # Instala Express.js en el proyecto.
node app.js # Inicia el servidor en el puerto por defecto (3000).
```

🌐 Accede en el navegador:

🔗 `http://IP_PUBLICA:3000`

### ⚡ Ejecución de un Proyecto Node.js con PM2

```sh
sudo npm install pm2 -g # Instala PM2 globalmente.
pm2 start app.js -i 0 # Inicia la aplicación en modo clúster con PM2.
pm2 stop app.js # Detiene la aplicación.
pm2 ls # Lista las aplicaciones en ejecución.
pm2 logs # Muestra los logs en tiempo real.
pm2 monit # Muestra métricas de rendimiento.
```

#### 📄 Configuración con ecosystem.config.js

##### 📌 1. Generar y Modificar el Archivo  

```sh
pm2 init simple # Crea un archivo ecosystem.config.js.
```

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

```sh
pm2 start ecosystem.config.js # Inicia la aplicación con configuración PM2.
pm2 restart ecosystem.config.js # Reinicia la aplicación.
pm2 reload ecosystem.config.js # Recarga sin tiempo de inactividad.
pm2 stop ecosystem.config.js # Detiene la aplicación.
pm2 delete ecosystem.config.js # Elimina la aplicación.
```

🎯 Ahora puedes desplegar y administrar aplicaciones Node.js con Express de manera eficiente.

---

## 🚀 Despliegue en Netlify

### 🛠 Instalación y Autenticación

Regístrate en [Netlify](https://www.netlify.com/) y luego instala la CLI globalmente:

```sh
sudo npm install -g netlify-cli
netlify login
```

### 🚀 Despliegue Manual

```sh
netlify deploy --prod
```
🔗 Tu aplicación estará disponible en `https://APP.netlify.app`

### 🔗 Despliegue Automático desde GitHub

1. **Crear y subir un repositorio**:
   ```sh
   git init
   git add .
   git commit -m "Subiendo el código..."
   git branch -M main
   git remote add origin https://github.com/username/repo.git
   git push -u origin main
   ```
2. **Importar desde Netlify**:
   - Accede a [Netlify](https://app.netlify.com/)
   - Selecciona "New Site from Git"
   - Conéctalo con tu cuenta de GitHub
   - Selecciona el repositorio y rama (`main`)
   - Configura el comando de build y carpeta de publicación
   - Pulsa "Deploy Site"

📌 Cada `git push` en `main` generará un nuevo despliegue automáticamente. 🎉

### ⚡ Despliegue de Aplicaciones Angular

```sh
sudo npm install -g @angular/cli
ng serve -o --host=IPPRIVADA
```
🔗 Accede en `http://IPPUBLICA:4200`