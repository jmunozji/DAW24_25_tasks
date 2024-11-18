---
title: 'CH3 - ABM'
---

# 🌐 Cheatsheet de Servidores Web: **Apache y Nginx**

---

## 🚀 **Instalación y Configuración Básica**

### **Apache**
🛠️ **Instalar**  
```bash
sudo apt update
sudo apt install apache2
```

🔧 **Habilitar y Verificar Servicio**  
```bash
sudo systemctl enable apache2
sudo systemctl start apache2
sudo systemctl status apache2
```

---

### **Nginx**
🛠️ **Instalar**  
```bash
sudo apt update
sudo apt install nginx
```

🔧 **Habilitar y Verificar Servicio**  
```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

---

## 🏗️ **Creación de Sitios Virtuales**

### **Apache**
1. 📄 **Crear archivo de configuración**:  
   ```bash
   sudo nano /etc/apache2/sites-available/mi_sitio.conf
   ```
   ✍️ **Contenido del archivo**:  
   ```apache
   <VirtualHost *:80>
       ServerName mi-sitio.com
       DocumentRoot /var/www/mi_sitio
   </VirtualHost>
   ```

2. ✅ **Activar y Reiniciar**:  
   ```bash
   sudo a2ensite mi_sitio.conf
   sudo systemctl reload apache2
   ```

---

### **Nginx**
1. 📄 **Crear archivo de configuración**:  
   ```bash
   sudo nano /etc/nginx/sites-available/mi_sitio
   ```
   ✍️ **Contenido del archivo**:  
   ```nginx
   server {
       listen 80;
       server_name mi-sitio.com;
       root /var/www/mi_sitio;
       index index.html;
   }
   ```

2. ✅ **Activar y Reiniciar**:  
   ```bash
   sudo ln -s /etc/nginx/sites-available/mi_sitio /etc/nginx/sites-enabled/
   sudo systemctl reload nginx
   ```

---

## 🔐 **Autenticación Básica**

### **Apache**
1. 📦 **Instalar herramientas**:  
   ```bash
   sudo apt install apache2-utils
   ```

2. 🔑 **Crear archivo de contraseñas**:  
   ```bash
   sudo htpasswd -c /etc/apache2/.htpasswd usuario
   ```

3. ⚙️ **Configurar autenticación**:  
   ```apache
   <Directory /var/www/mi_sitio>
       AuthType Basic
       AuthName "Acceso restringido"
       AuthUserFile /etc/apache2/.htpasswd
       Require valid-user
   </Directory>
   ```

4. ✅ **Reiniciar servicio**:  
   ```bash
   sudo systemctl reload apache2
   ```

---

### **Nginx**
1. 🔑 **Crear archivo de contraseñas**:  
   ```bash
   sudo apt install apache2-utils
   sudo htpasswd -c /etc/nginx/.htpasswd usuario
   ```

2. ⚙️ **Configurar autenticación**:  
   ```nginx
   location / {
       auth_basic "Acceso restringido";
       auth_basic_user_file /etc/nginx/.htpasswd;
   }
   ```

3. ✅ **Reiniciar servicio**:  
   ```bash
   sudo systemctl reload nginx
   ```

---

## 🔒 **Habilitar HTTPS**

### **Apache**
1. ⚡ **Activar módulo SSL**:  
   ```bash
   sudo a2enmod ssl
   ```

2. 📜 **Crear certificado autofirmado**:  
   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
   -keyout /etc/ssl/private/mi_sitio.key -out /etc/ssl/certs/mi_sitio.crt
   ```

3. ⚙️ **Configurar Virtual Host con HTTPS**:  
   ```apache
   <VirtualHost *:443>
       ServerName mi-sitio.com
       DocumentRoot /var/www/mi_sitio
       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/mi_sitio.crt
       SSLCertificateKeyFile /etc/ssl/private/mi_sitio.key
   </VirtualHost>
   ```

4. ✅ **Reiniciar servicio**:  
   ```bash
   sudo systemctl reload apache2
   ```

---

### **Nginx**
1. 📜 **Crear certificado autofirmado**:  
   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
   -keyout /etc/nginx/mi_sitio.key -out /etc/nginx/mi_sitio.crt
   ```

2. ⚙️ **Configurar servidor HTTPS**:  
   ```nginx
   server {
       listen 443 ssl;
       server_name mi-sitio.com;
       ssl_certificate /etc/nginx/mi_sitio.crt;
       ssl_certificate_key /etc/nginx/mi_sitio.key;
       root /var/www/mi_sitio;
       index index.html;
   }
   ```

3. ✅ **Reiniciar servicio**:  
   ```bash
   sudo systemctl reload nginx
   ```

---

## 🖥️ **Pila LAMP**

1. 🛠️ **Instalar paquetes esenciales**:  
   ```bash
   sudo apt update
   sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql
   ```

2. ✅ **Verificar versiones**:  
   ```bash
   apache2 -v
   mysql --version
   php -v
   ```

---

## 🐳 **Pila LAMP con Docker**

1. 📄 **Crear archivo `docker-compose.yml`**:  
   ```yaml
   version: '3.8'
   services:
     web:
       image: php:apache
       ports:
         - "8080:80"
       volumes:
         - ./mi_sitio:/var/www/html
     db:
       image: mysql:latest
       environment:
         MYSQL_ROOT_PASSWORD: rootpass
         MYSQL_DATABASE: mi_base
         MYSQL_USER: user
         MYSQL_PASSWORD: userpass
   ```

2. ▶️ **Iniciar contenedores**:  
   ```bash
   docker-compose up -d
   ```

3. 🌐 **Acceso al sitio web**:  
   Visita: [http://localhost:8080](http://localhost:8080)
