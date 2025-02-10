---
title: 'CH6 - ALS'
---

# 🎛️ **Servidores de Aplicaciones**  

---

## 🐱 **Apache Tomcat**

### 🔧 **Instalación en Linux (Ubuntu/Debian)**  
1. **Actualizar repositorios:**  
   ```bash
   sudo apt update && sudo apt upgrade
   ```  
2. **Instalar Java:**  
   ```bash
   sudo apt install default-jdk default-jre
   ```  
3. **Instalar Tomcat:**  
   ```bash
   sudo apt install -y tomcat10 tomcat10-admin
   ```  
4. **Verificar estado del servicio:**  
   ```bash
   systemctl status tomcat10
   ```  
5. **Iniciar y habilitar el servicio:**  
   ```bash
   sudo systemctl start tomcat10
   sudo systemctl enable tomcat10
   ```  

### 🛠️ **Configuración de Usuario en Tomcat**
1. **Editar el archivo `tomcat-users.xml`:**  
   ```bash
   sudo nano /etc/tomcat10/tomcat-users.xml
   ```  
2. **Añadir las siguientes líneas:**  
   ```xml
   <role rolename="manager-gui"/>
   <role rolename="admin-gui"/>
   <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
   ```  
3. **Reiniciar Tomcat:**  
   ```bash
   sudo systemctl restart tomcat10
   ```  

### 🚀 **Despliegue de Aplicaciones en Tomcat**  
1. **Copiar archivos `.war` en el directorio de despliegue:**  
   ```bash
   sudo cp <app>.war /var/lib/tomcat10/webapps/
   ```  
2. **Verificar despliegue:**  
   - URL: `http://localhost:8080/<app>`  
3. **Reiniciar Tomcat tras cambios:**  
   ```bash
   sudo systemctl restart tomcat10
   ```  

### 🛠️ **Despliegue en Tomcat con Maven**  
1. **Instalar Maven:**  
   ```bash
   sudo apt install maven
   ```  
2. **Añadir el plugin de Tomcat en `pom.xml`:**  
   ```xml
   <plugin>
       <groupId>org.apache.tomcat.maven</groupId>
       <artifactId>tomcat7-maven-plugin</artifactId>
       <version>2.2</version>
   </plugin>
   ```  
3. **Desplegar la aplicación:**  
   ```bash
   mvn tomcat7:deploy
   ```  
4. **Acceder a la aplicación:**  
   - URL: `http://localhost:8080/<artifactId>`  

---

## 🌐 **Node.js y Express**

### 🚀 **Despliegue Básico**  
1. **Instalar Node.js:**  
   ```bash
   sudo apt install nodejs npm
   ```  
2. **Crear un proyecto Express:**  
   ```bash
   mkdir myapp && cd myapp
   npm init -y
   npm install express
   ```  
3. **Código básico (`app.js`):**  
   ```javascript
   const express = require('express');
   const app = express();

   app.get('/', (req, res) => {
       res.send('¡Hola Mundo!');
   });

   app.listen(3000, () => {
       console.log('Servidor en http://localhost:3000');
   });
   ```  
4. **Ejecutar la aplicación:**  
   ```bash
   node app.js
   ```  

### 🔄 **Despliegue Clusterizado con Node Express**  
1. **Instalar clúster:**  
   ```bash
   npm install cluster
   ```  
2. **Código de clúster (`app-cluster.js`):**  
   ```javascript
   const cluster = require('cluster');
   const os = require('os');
   const express = require('express');

   if (cluster.isMaster) {
       const numCPUs = os.cpus().length;
       for (let i = 0; i < numCPUs; i++) {
           cluster.fork();
       }
   } else {
       const app = express();
       app.get('/', (req, res) => {
           res.send(`¡Hola desde el clúster! PID: ${process.pid}`);
       });
       app.listen(3000);
   }
   ```  

---

## ☁️ **Netlify (PaaS)**

### 🛠️ **Despliegue en Netlify**  
1. **Instalar CLI:**  
   ```bash
   npm install -g netlify-cli
   ```  
2. **Iniciar sesión:**  
   ```bash
   netlify login
   ```  
3. **Desplegar proyecto:**  
   ```bash
   netlify deploy
   ```  
4. **Despliegue en producción:**  
   ```bash
   netlify deploy --prod
   ```  

### 📂 **Despliegue mediante conexión con GitHub**  
1. **Crear repositorio en GitHub y subir el código:**  
   ```bash
   git init
   git add .
   git commit -m "Subiendo el código..."
   git branch -M main
   git remote add origin https://github.com/username/proyecto.git
   git push -u origin main
   ```  
2. **Importar proyecto en Netlify desde GitHub y desplegarlo.**  

---

Este cheatsheet proporciona una referencia rápida y estructurada para la instalación, configuración y despliegue de aplicaciones en **Tomcat**, **Node.js con Express**, y **Netlify**. 🚀

