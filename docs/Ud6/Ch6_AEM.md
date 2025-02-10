---
title: 'CH6 - AEM'
---

# 📋 **Cheat Sheet: Servidores de Aplicaciones**

---

## 🚀 **1. Instalación de Tomcat**

### **Comandos Básicos**

| **Acción**              | **Comando**                                  | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Descargar Tomcat        | `wget https://downloads.apache.org/tomcat/...` | Descarga la última versión de Tomcat.      |
| Extraer archivo         | `tar -xvzf apache-tomcat-*.tar.gz`            | Extrae el archivo comprimido.              |
| Mover carpeta           | `sudo mv apache-tomcat-* /opt/tomcat`         | Mueve Tomcat a la carpeta deseada.         |
| Dar permisos de ejecución | `chmod +x /opt/tomcat/bin/*.sh`               | Asigna permisos de ejecución a los scripts. |
| Iniciar Tomcat          | `/opt/tomcat/bin/startup.sh`                 | Inicia el servidor Tomcat.                 |
| Detener Tomcat          | `/opt/tomcat/bin/shutdown.sh`                | Detiene el servidor Tomcat.                |

### **Archivos de Configuración**
- **`conf/server.xml`**: Configuración de puertos y servicios.
- **`conf/tomcat-users.xml`**: Gestión de usuarios y roles.

---

## 📦 **2. Despliegue en Tomcat**

| **Acción**              | **Comando/Proceso**                         | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Copiar archivo WAR      | `cp app.war /opt/tomcat/webapps/`            | Despliega una aplicación WAR.              |
| Ver logs de Tomcat      | `tail -f /opt/tomcat/logs/catalina.out`      | Muestra los logs en tiempo real.           |
| Reiniciar Tomcat        | `/opt/tomcat/bin/shutdown.sh && /opt/tomcat/bin/startup.sh` | Reinicia el servidor para aplicar cambios. |

---

## 🛠️ **3. Uso de Maven con Tomcat**

| **Acción**              | **Comando**                                 | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Compilar proyecto       | `mvn clean install`                         | Compila el proyecto y genera el archivo WAR. |
| Desplegar con Tomcat Plugin | `mvn tomcat7:deploy`                        | Despliega la aplicación directamente desde Maven. |

### **`pom.xml` (Ejemplo de configuración para Tomcat)**
```xml
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <url>http://localhost:8080/manager/text</url>
        <server>TomcatServer</server>
        <path>/miapp</path>
    </configuration>
</plugin>
```

---

## 🌐 **4. Node.js y Express**

### **Comandos Básicos**

| **Acción**              | **Comando**                                  | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Inicializar proyecto    | `npm init -y`                                | Crea un `package.json` predeterminado.     |
| Instalar Express        | `npm install express`                       | Instala Express.js.                        |
| Ejecutar servidor       | `node app.js`                                | Inicia la aplicación Node.js.              |

### **Ejemplo de Servidor Express**
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('¡Hola Mundo!');
});

app.listen(3000, () => {
    console.log('Servidor escuchando en http://localhost:3000');
});
```

---

## ⚡ **6. Configuración de Cluster (PM2)**

### **Comandos PM2**

| **Acción**              | **Comando**                                  | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Instalar PM2            | `npm install pm2 -g`                        | Instala PM2 globalmente.                   |
| Iniciar app con PM2     | `pm2 start app.js`                           | Ejecuta la app en modo clúster.            |
| Ver procesos            | `pm2 list`                                   | Muestra los procesos gestionados por PM2.  |
| Detener proceso         | `pm2 stop app`                               | Detiene una aplicación específica.         |
| Reiniciar app           | `pm2 restart app`                            | Reinicia la aplicación.                    |

---

## 🌍 **7. Despliegue en Netlify**

### **Comandos Básicos**

| **Acción**              | **Comando**                                  | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Instalar Netlify CLI    | `npm install -g netlify-cli`                | Instala la CLI de Netlify.                 |
| Login en Netlify        | `netlify login`                              | Inicia sesión en tu cuenta de Netlify.     |
| Desplegar sitio         | `netlify deploy --prod`                      | Despliega el sitio en producción.          |
| Ver estado del sitio    | `netlify status`                             | Muestra el estado actual del despliegue.   |

---

## 🐍 **8. Flask (Python)**

### **Comandos Básicos**

| **Acción**              | **Comando**                                  | **Descripción**                            |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Instalar Flask          | `pip install flask`                         | Instala el framework Flask.                |
| Ejecutar aplicación     | `flask run`                                  | Inicia el servidor Flask.                  |
| Definir variable de entorno | `export FLASK_APP=app.py`                   | Define el archivo principal de la app.     |

### **Ejemplo de App Flask**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "¡Hola desde Flask!"

if __name__ == '__main__':
    app.run(debug=True)
```

---

## ✅ **Consejos Finales**
- **Revisar Logs** siempre que haya errores.
- **Reiniciar Servicios** después de cambios importantes.
- **Documentar** cualquier configuración personalizada.

¡Este Cheat Sheet te ayudará a recordar los comandos más importantes durante tus prácticas! 🚀

