---
title: 'CH6 - RSN'
---

## Instalación de Tomcat ⏬🐱

| Comandos 🛡️ | Explicación 🗨️ |
-------------------- | -----------------------
| `sudo apt-get install default-jdk` | Instalación del paquete necesario para java |
| `sudo apt-get install default-jre` | Instalación de Java |
| `sudo apt-get install -y tomcat10 tomcat10-admin` | Instalación de Tomcat |
| `sudo nano /etc/tomcat10/tomcat-users.xml` | Creación de archivo de gestión de usuarios |


## Despliegue de aplicaciones con Tomcat ⏫🐱

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `ls -l /var/lib/tomcat10/webapps/sample` | Comprobar estructura de directorios de tomcat |
| `sudo passwd tomcat` | Asignar contraseña al usuario tomcat |
| `mkdir -p /var/lib/tomcat10/webapps/prueba/WEB-INF/classes` | Crear estructura de carpetas |
| `javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java` | Compilación de la aplicación de java |
| `  mv hola1.class ./WEB-INF/classes` | Mover la clase a su respectiva estructura |


## Despliegue de Tomcat con Maven 🐱🪶

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo apt-get install maven` | Instalación de Maven |
| `sudo nano /etc/maven/settings.xml` | Modificación del archivo de settings |
| `mvn tomcat7:deploy` | Construir la aplicación |
| `mvn tomcat7:redeploy` | Volver a desplegar la aplicación |
| `mvn tomcat7:undeploy` | Replegar la aplicación |
| `mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false` | Crear estructura de carpetas |


## Despliegue de aplicaciones con Node ⏫♻️

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- 
| `sudo apt-get install -y ca-certificates curl gnupg` | Importar clave GPG de Nodesource |
| `sudo mkdir -p /etc/apt/keyrings` | Creación de carpeta para la "key" |
| `curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg` | Importación final de la clave |
| `echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" sudo tee /etc/apt/sources.list.d/nodesource.list` | Creación del repositorio deb |
| `sudo apt-get install nodejs -y` | Instalación de Node |
| `sudo npm install -g express` | Instalación de Express |
| `sudo npm install -g npm@10.1.0` | Instalación de Node versión actual (depende de la salida por consola)|
| `npm init -y` | Iniciar proyecto |
| `npm install express` | Instalar express de forma local|
| `npm run start:dev` | Iniciar aplicación |


## Dockerización del despliegue de una aplicación con Node 📦♻️
| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `docker build -t the-example-app.nodejs .` | Construir proyecto |
| `docker run -p 3000:3000 -d the-example-app.nodejs` | Iniciar el contenedor |


## Despliegue de una aplicación "clusterizada" con Node 🐢♻️
| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo npm install -g loadtest` | Instalación de loadtest |
| `loadtest http://localhost:3000/api/500000 -n 1000 -c 100` | Realizar la prueba de carga |
| `sudo npm install pm2 -g` | Instalación de PM2 |
| `pm2 start nombre_aplicacion_sin_cluster.js -i 0` | Ejecución de la aplicación |
| `ps -ef  grep .js` | Comprobación de que hay 2 procesos en funcionamiento |
| `pm2 stop app.js` | Detener la aplicación |


## Despliegue de una aplicación en Netlify 🔁⏫
| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo npm install netlify-cli -g` | Instalación de netlify |
| `netlify login` | Loguearse con netlify |
| `netlify deploy` | Pre-deploy de Netlify |
| `sudo npm install -g @angular/cli` | Instalar cliente de angular |
| `ng serve -o --host=IPPRIVADA` | Abrir servidor |


## Configuraciones de archivos 📜


```xml
> Archivo configuración tomcat-users.xml (P1)

  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

```xml
> Archivo configuración web.xml

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



```xml
> Archivo configuración tomcat-users.xml

     <role rolename="admin-gui"/>
     <role rolename="manager-gui"/>
     <role rolename="manager-script"/>
     <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
     <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
```


```xml
> Archivo configuración settins.xml

      <server>
      <id>DesplieguesTomcat</id>
      <username>despliegues</username>
      <password>ieselcaminas</password>
    </server>
```


```xml
> Añadido a pom.xml

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





```xml
> Añadido a pom.xml

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