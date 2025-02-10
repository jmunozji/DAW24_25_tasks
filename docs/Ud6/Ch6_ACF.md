---
title: 'Ch6 - ACF'
---

# 🌐 Servidores de aplicaciones

### 1. ⚙️ Comandos de instalación y comprobación

| Comando | Explicación |
|----------|----------|
| `sudo apt-get install default-jdk` | ☕ Instala Java JDK |
| `sudo apt-get install default-jre` | ☕ Instala Java JRE |
| `java --version` | 🔍 Verifica la versión de Java instalada |
| `sudo apt-get install -y tomcat10 tomcat10-admin` | 🏗️ Instala Tomcat |
| `sudo apt-get install maven` | ⚒️ Instala Maven |
| `mvn --version` | 🔍 Verifica la versión instalada de Maven |
| `sudo apt-get install -y ca-certificates curl gnupg` | 🔑 Descarga la clave GPG de NodeSource |
| `sudo apt-get install nodejs -y` | 🟢 Instala Node.js |
| `node --version` | 🔍 Verifica la versión instalada de Node.js |
| `sudo npm install -g express` | 🚀 Instala Express globalmente |
| `sudo npm install -g npm@10.1.0` | 📦 Actualiza npm |
| `sudo npm install -g loadtest` | 📊 Instala Loadtest para pruebas de carga |
| `sudo npm install pm2 -g` | 🔄 Instala PM2 para gestionar procesos Node.js |
| `sudo npm install netlify-cli -g` | ☁️ Instala Netlify CLI |
| `sudo apt-get install python3-pip` | 🐍 Instala pip para Python |
| `sudo apt-get install pipenv` | 📦 Instala pipenv para entornos virtuales |
| `pipenv --version` | 🔍 Verifica la versión instalada de pipenv |

### 2. 🚀 Comandos de configuración y despliegue Maven

| Comando | Explicación |
|----------|----------|
| `mvn tomcat7:deploy` | 🚀 Despliega la aplicación en Tomcat |
| `mvn tomcat7:redeploy` | 🔄 Redespliega la aplicación en Tomcat |
| `mvn tomcat7:undeploy` | 🛑 Retira la aplicación de Tomcat |
| `mvn archetype:generate -DgroupId=IESElCaminas -DartifactId=miapp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false` | 🏗️ Crea una aplicación Java de prueba desde cero |
| `sudo dpkg-reconfigure slapd` | ⚙️ Configura el directorio LDAP |

📌 **Ejemplo para activar la interfaz gráfica del manager de Tomcat:**
```
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>

```

### 3. 🟢 Comandos de configuración y despliegue Node.js

| Comando | Explicación |
|----------|----------|
| `npm init -y` | 📂 Inicializa un nuevo proyecto Node.js |
| `npm install express` | ⚡ Instala Express de manera local |
| `node app.js` | ▶️ Ejecuta el proyecto |
| `npm install` | 📦 Instala las librerías necesarias |
| `npm run start:dev` | 🚀 Inicia la aplicación en modo desarrollo |

### 4. ☁️ Comandos de configuración y despliegue Netlify

| Comando | Explicación |
|----------|----------|
| `netlify login` | 🔑 Inicia sesión en Netlify |
| `npm install` | 📦 Instala las librerías necesarias |
| `npm run start` | ▶️ Inicia la aplicación |
| `npm run build` | 🏗️ Genera un build para producción |
| `netlify deploy` | 🚀 Despliega la aplicación en Netlify |

### 5. 🐍 Comandos de configuración y despliegue Flask

| Comando | Explicación |
|----------|----------|
| `pipenv shell` | 📂 Inicia el entorno virtual |
| `pipenv install flask gunicorn` | 🔧 Instala Flask y Gunicorn |
| `gunicorn --workers 2 --bind 0.0.0.0:5000 wsgi:app` | 🚀 Comprueba que Gunicorn funciona correctamente |
