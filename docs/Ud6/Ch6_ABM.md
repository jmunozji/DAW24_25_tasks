---
title: 'CH6 - ABM'
---


# 🎛️ **Cheatsheet: Servidores de Aplicaciones**  

---

## 🐱 **Apache Tomcat**

### 🔧 **Instalación en Linux (Ubuntu/Debian)**  
1. **Actualizar repositorios:**  
   ```bash
   sudo apt update
   ```  
2. **Instalar Tomcat:**  
   ```bash
   sudo apt install tomcat10 tomcat10-admin
   ```  
3. **Verificar estado del servicio:**  
   ```bash
   sudo systemctl status tomcat10
   ```  
4. **Iniciar el servicio:**  
   ```bash
   sudo systemctl start tomcat10
   ```  
5. **Habilitar en el arranque:**  
   ```bash
   sudo systemctl enable tomcat10
   ```

---

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

---

### 🛠️ **Despliegue en Tomcat con Maven**  
1. **Agregar el plugin de Tomcat en `pom.xml`:**
   ```xml
   <plugin>
       <groupId>org.apache.tomcat.maven</groupId>
       <artifactId>tomcat7-maven-plugin</artifactId>
       <version>2.2</version>
   </plugin>
   ```
2. **Desplegar la aplicación:**  
   ```bash
   mvn tomcat7:deploy
   ```
3. **Acceder a la aplicación:**  
   - URL: `http://localhost:8080/<artifactId>`

---

## 🌐 **Node.js y Express**

### 🚀 **Despliegue Básico**  
1. **Crear un proyecto Express:**  
   ```bash
   mkdir myapp && cd myapp
   npm init -y
   npm install express
   ```
2. **Código básico (`app.js`):**  
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
3. **Ejecutar la aplicación:**  
   ```bash
   node app.js
   ```

---

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
