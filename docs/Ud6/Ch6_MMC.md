---
title: 'CH6 - MMC'
---



# CheatSheet Servidores de Aplicaciones

## Instalaciones 🔍

| sudo apt-get install default-jdk
sudo apt-get install default-jre | Instalar Java |
| --- | --- |
| sudo apt-get install -y tomcat10 tomcat10-admin | Instalación Apache Tomcat  |
| sudo apt-get install maven |  Instalar Maven |
| sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg | Instalar GPG de Nodesource |
| sudo apt-get install nodejs -y | Instalar Node.js |
| sudo npm install -g express | Instalar Express |
| sudo npm install -g loadtest | Instalar loadtest, para medir el rendimiento |
| sudo npm install netlify-cli -g | Instalar Netlify CLI |
| sudo npm install -g @angular/cli | Instalar Angular CLI |
| sudo apt-get install python3-pip | Instalar Python |
| sudo apt-get install pipenv | Instalar el gestor de entornos virtuales de Python |

## Configuración Tomcat ☁️

|   <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/> | Creación de un usuario dentro de “tomcat-users.xml” |
| --- | --- |
| javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java | Compilar aplicación java |
| <?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "[http://java.sun.com/dtd/web-app_2_3.dtd](http://java.sun.com/dtd/web-app_2_3.dtd)">
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
</web-app> | Configuración del fichero “web.xml” |

## Configuración Tomcat con Maven *⛈️*

| <role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
<user username="despliegues" password="ieselcaminas" roles="manager-script"/> | Modificación “tomcat-users.xml” |
| --- | --- |
|     <server>
      <id>DesplieguesTomcat</id>
      <username>despliegues</username>
      <password>ieselcaminas</password>
    </server> | Modificación “settings.xml” |
| <build>
  <finalName>roshambo</finalName> #
  <plugins>
   <plugin>
   <groupId>org.apache.tomcat.maven</groupId>
   <artifactId>tomcat7-maven-plugin</artifactId>
   <version>2.2</version>
   <configuration>
     <url>[http://localhost:8080/manager/text](http://localhost:8080/manager/text)</url> #
     <server>DesplieguesTomcat</server> #
     <path>/rps</path> #
    </configuration>
    </plugin>
   </plugins>
</build> | Modificación “pom” |
| mvn tomcat7:deploy | Desplegar en Maven |
| mvn tomcat7:redeploy | Volver a desplegar en Maven |
| mvn tomcat7:undeploy | Replegar en Maven |
|     mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false | Creación de un proyecto nuevo desde Maven |

## Despliegue con Node Express 🚄

| npm init -y | Inicializar proyecto |
| --- | --- |
| npm install express | Instalar Express en el proyecto |
| node app.js | Ejecutar el proyecto |
| npm install | Instalar librerías necesarias |
| npm run start:dev | Iniciar la aplicación |

## Despliegue con Netlify 📚

| netlify login | Autentificación |
| --- | --- |
| npm install | Instalar dependencias |
| npm run start | Ejecutar el script “start” |
| npm run build | Buildear la aplicación |
| netlify deploy | Prepara la aplicación para subirla |
| ng serve -o --host=IPPRIVADA | Poner en funcionamiento la aplicación si es de ANGULAR |

## Despliegue con Flask (Python) 🐳

| pipenv shell | Iniciar entorno virtual |
| --- | --- |
| pipenv install flask gunicorn | Instalar dependencias |
| flask run —host ‘0.0.0.0’ | Ejecutar aplicación |
| gunicorn --workers 2 --bind 0.0.0.0:5000 wsgi:app | Comprobar si funciona la app |
| which gunicorn | Mostrar ruta donde se ejecuta Gunicorn |
| deactivate | Salir del entorno virtual |
