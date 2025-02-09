### 📌 **Servidores de Aplicaciones y Despliegue de Aplicaciones Web**

---

## 🔹 **Servidores de Aplicaciones**

Los **servidores de aplicaciones** son plataformas que ejecutan aplicaciones web y gestionan la lógica de negocio. Funcionan como intermediarios entre el **servidor web** y la **base de datos**, facilitando procesos como la autenticación, seguridad, manejo de sesiones y conexiones.

### **Funciones principales:**
- ✅ Procesar solicitudes dinámicas.
- ✅ Manejar transacciones y comunicación entre servicios.
- ✅ Mejorar la escalabilidad y seguridad de las aplicaciones.

---

### **Diferencias entre Servidor Web y Servidor de Aplicaciones**

| **Característica**  | **Servidor de Aplicaciones**         | **Servidor Web**               |  
|---------------------|--------------------------------------|--------------------------------|  
| **Propósito**       | Manejo de lógica de negocio y datos. | Entrega de contenido estático. |  
| **Uso de recursos** | Mayor consumo de CPU y memoria.      | Ligero y rápido.               |  
| **Ejemplos**        | Tomcat, JBoss, WebLogic.             | Apache, Nginx.                 |  

---

## 🔹 **Despliegue de Aplicaciones Web**

El **despliegue** es el proceso de mover una aplicación desde el entorno de desarrollo hasta su ejecución en producción.

### **Fases del despliegue:**
1. **Desarrollo** → Creación y pruebas iniciales en local.
2. **Pruebas** → Validación en un entorno de testeo.
3. **Preproducción** → Última verificación antes de la salida a producción.
4. **Producción** → Implementación final accesible para los usuarios.
5. **Monitoreo** → Supervisión para detectar errores y optimizar rendimiento.

---

### **Tipos de despliegue:**
- **Código:** Archivos y scripts (Java, JavaScript, CSS, etc.).
- **Contenido:** Imágenes, textos y configuraciones.

---

### **Mejores prácticas:**
- ✔ **Uso de Git:** Control de versiones para evitar errores.
- ✔ **Entornos separados:** Desarrollo, pruebas y producción.
- ✔ **Automatización:** Uso de scripts y herramientas como Jenkins o Docker.
- ✔ **Supervisión post-despliegue:** Para detectar problemas rápidamente.

---

## 🔹 **Despliegue de Aplicaciones Java**

Las aplicaciones Java suelen desplegarse en servidores de aplicaciones como **Tomcat** o **JBoss**, utilizando archivos **.jar** o **.war**.

### **Herramientas clave:**
- **Maven:** Manejo de dependencias y automatización del build.
- **WAR (Web Application Archive):** Paquete que contiene HTML, servlets y configuración.

### **Ejemplo de comandos Maven:**
```sh
mvn compile       # Compila el código fuente  
mvn package       # Empaqueta en .jar o .war  
mvn deploy        # Despliega la aplicación  
```

---

## 🔹 **Despliegue de Aplicaciones JavaScript y Node.js**

Las aplicaciones JavaScript pueden ejecutarse tanto en el **cliente** (frontend) como en el **servidor** con **Node.js**.

### **Frontend:**
Se sirve a través de **Apache, Nginx, Vercel o Netlify**.

### **Backend:**
Se ejecuta en servidores con **Node.js + Express**.

---

### **¿Qué es Node.js?**
Es un entorno de ejecución de JavaScript en el servidor. No es un lenguaje ni un framework, sino una plataforma para ejecutar código JS fuera del navegador.

---

### **¿Qué es Express.js?**
Es un **framework de Node.js** que facilita la creación de APIs y aplicaciones web, gestionando rutas, peticiones y middleware.

---

### **¿Qué es npm?**
**npm (Node Package Manager)** es el gestor de paquetes de JavaScript que permite instalar librerías y gestionar dependencias.

### **Archivo `package.json`:**
Contiene la configuración de un proyecto Node.js, incluyendo nombre, versión y scripts automatizados.

### **Ejemplo de NPM scripts en `package.json`:**
```json
{
  "scripts": {
    "start": "node app.js",
    "test": "jest",
    "build": "webpack"
  }
}
```

---

## 🔹 **Prácticas de Despliegue**

### **Práctica 1: Instalación de Tomcat**

1. **Crear máquina virtual EC2 (Debian):**
    - **Nombre:** PTomcat.
    - **Puerto 8080:** Permitir acceso HTTP/HTTPS.

2. **Instalar Java:**
   ```bash
   sudo apt-get update && sudo apt-get upgrade
   sudo apt-get install default-jdk default-jre
   ```

3. **Instalar Tomcat:**
   ```bash
   sudo apt-get install -y tomcat10 tomcat10-admin
   systemctl status tomcat10
   ```

4. **Crear usuario de administración:**
   ```bash
   sudo nano /etc/tomcat10/tomcat-users.xml
   ```
   Añadir:
   ```xml
   <role rolename="manager-gui"/>
   <role rolename="admin-gui"/>
   <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
   ```
   Reiniciar Tomcat:
   ```bash
   sudo systemctl restart tomcat10
   ```

---

### **Práctica 2: Despliegue de aplicaciones con Tomcat**

1. **Despliegue manual de una aplicación WAR:**
    - Acceder al Gestor de Aplicaciones Web en `http://IP_SERVIDOR:8080/manager`.
    - Subir y desplegar el archivo `.war`.

2. **Desplegar una aplicación nueva paso a paso:**
    - Crear estructura de carpetas en `/var/lib/tomcat10/webapps/prueba/WEB-INF/classes`.
    - Compilar archivos Java:
      ```bash
      javac -classpath /usr/share/tomcat10/lib/servlet-api.jar hola1.java
      ```
    - Configurar `web.xml`:
      ```xml
      <servlet>
          <servlet-name>hola1</servlet-name>
          <servlet-class>hola1</servlet-class>
      </servlet>
      <servlet-mapping>
          <servlet-name>hola1</servlet-name>
          <url-pattern>/hola1</url-pattern>
      </servlet-mapping>
      ```

---

### **Práctica 3: Despliegue en Tomcat con Maven**

1. **Instalar Maven:**
   ```bash
   sudo apt-get install maven
   ```

2. **Configurar Maven y Tomcat:**
    - Crear usuario en `tomcat-users.xml`.
    - Configurar servidor en `settings.xml`.

3. **Desplegar proyecto con Maven:**
   ```bash
   mvn tomcat7:deploy
   ```

---

### **Práctica 4: Despliegue de aplicaciones con Node Express**

1. **Instalar Node.js y Express:**
   ```bash
   sudo npm install -g express
   ```

2. **Crear proyecto con Express:**
   ```bash
   npm init -y
   npm install express
   ```

3. **Desplegar aplicación:**
   ```bash
   node app.js
   ```

---

### **Práctica 5: Dockerización del despliegue de una aplicación Node.js**

1. **Crear Dockerfile:**
   ```Dockerfile
   FROM node
   WORKDIR /app
   COPY . /app
   RUN npm install
   EXPOSE 3000
   CMD ["node", "app.js"]
   ```

2. **Construir y ejecutar contenedor:**
   ```bash
   docker build -t my-node-app .
   docker run -p 3000:3000 -d my-node-app
   ```

---

### **Práctica 6: Despliegue de una aplicación "clusterizada" con Node Express**

1. **Usar módulo Cluster:**
   ```javascript
   const cluster = require('cluster');
   if (cluster.isMaster) {
     for (let i = 0; i < totalCPUs; i++) {
       cluster.fork();
     }
   } else {
     // Código de la aplicación
   }
   ```

2. **Usar PM2 para gestión del clúster:**
   ```bash
   pm2 start app.js -i 0
   ```

---

### **Práctica 7: Despliegue de una aplicación en Netlify (PaaS)**

1. **Instalar Netlify CLI:**
   ```bash
   sudo npm install netlify-cli -g
   ```

2. **Desplegar aplicación:**
   ```bash
   netlify deploy --prod
   ```

---

### **Práctica 8: Despliegue de una aplicación Flask (Python)**

1. **Instalar Flask y Gunicorn:**
   ```bash
   pipenv install flask gunicorn
   ```

2. **Configurar servicio Gunicorn:**
   ```bash
   systemctl enable flask_app
   systemctl start flask_app
   ```

---

### **Práctica 9: Aplicación Python con Flask en Docker**

1. **Crear Dockerfile:**
   ```Dockerfile
   FROM python:3.8-slim
   WORKDIR /app
   COPY . /app
   RUN pip install -r requirements.txt
   EXPOSE 80
   CMD ["gunicorn", "--bind", "0.0.0.0:80", "wsgi:app"]
   ```

2. **Construir y ejecutar contenedor:**
   ```bash
   docker build -t my-flask-app .
   docker run -p 4000:80 -d my-flask-app
   ```

---