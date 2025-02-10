---
title: 'CH6 - MCG'
---

# unidad 6 - servidores de aplicación

## Tomcat

### Actualizar el sistema
```bash
sudo apt-get update && sudo apt-get upgrade
```

Instalar Java

```bash
sudo apt-get install default-jdk
sudo apt-get install default-jre
```

Instalar Tomcat 10 y Maven
```bash
sudo apt-get install -y tomcat10 tomcat10-admin
sudo apt-get install maven
```
Configuración en tomcat-users.xml (para acceso administrativo)

```xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="s3cret" roles="admin-gui,manager-gui"/>
```

Crear un proyecto web con Maven

```bash
mvn archetype:generate -DgroupId=$nombre -DartifactId=$nombreProyecto -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

Comandos para desplegar, redeplegar y desinstalar en Tomcat

```bash
mvn tomcat7:deploy
mvn tomcat7:redeploy
mvn tomcat7:undeploy
```

## Node.js
Actualizar el sistema
```bash
sudo apt-get update
```
Instalar dependencias necesarias
```bash
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
```
Agregar el repositorio de Node.js
```bash
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

Instalar Node.js

```bash
sudo apt-get update
sudo apt-get install nodejs -y
```

Instalar Express.js

```bash
sudo npm install -g express
```
Crear un proyecto Node.js e instalar dependencias

```bash
npm init -y
npm install express
npm run start:dev
```

Instalar Netlify CLI y realizar despliegue

```bash
sudo npm install netlify-cli -g
netlify login
netlify deploy
```

## Flask
Instalar PIP y Pipenv

```bash
sudo apt-get install python3-pip
sudo apt-get install pipenv
```

Instalar Flask y Gunicorn

```bash
pipenv install flask gunicorn
```

Iniciar la aplicación Flask

```bash
systemctl start flask_app
```
