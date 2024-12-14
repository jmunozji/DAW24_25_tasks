---
title: 'CH3 - MIP'
---

# Cheatsheet para Instalación y Configuración de Servidores Web y Pila LAMP

## **Configuración Inicial del Servidor**

1. **Preparar el entorno**:
   - Configurar una máquina virtual (VM) o instancia en la nube con:
     - Sistema operativo: Debian (o similar basado en Linux).
     - Puertos abiertos según necesidades (SSH, HTTP, HTTPS, etc.).
     - Actualizar sistema operativo:
       ```bash
       sudo apt update && sudo apt upgrade
       ```

2. **Configurar acceso SSH seguro**:
   - Crear o usar un par de claves existente.
   - Configurar grupos de seguridad para limitar el acceso a los puertos necesarios.

---

## **Instalación de Servidor Web**

1. **Instalar Nginx o Apache**:
   - Para **Nginx**:
     ```bash
     sudo apt install nginx
     sudo systemctl start nginx
     ```
   - Para **Apache**:
     ```bash
     sudo apt install apache2
     sudo systemctl start apache2
     ```

2. **Configurar directorios y permisos**:
   - Crear directorios para los sitios web:
     ```bash
     sudo mkdir -p /var/www/sitio1
     ```
   - Ajustar propietarios y permisos:
     ```bash
     sudo chown -R www-data:www-data /var/www/sitio1
     ```

3. **Habilitar sitios virtuales**:
   - Crear un archivo de configuración para cada sitio en `/etc/nginx/sites-available` o `/etc/apache2/sites-available`.
   - Activar sitios y reiniciar el servicio:
     ```bash
     sudo ln -s /etc/nginx/sites-available/sitio1 /etc/nginx/sites-enabled/
     sudo nginx -t
     sudo systemctl restart nginx
     ```

---

## **Habilitar Autenticación en el Servidor Web**

1. **Instalar herramientas de autenticación**:
   ```bash
   sudo apt install apache2-utils
   ```

2. **Crear archivo de contraseñas**:
   ```bash
   sudo htpasswd -c /etc/nginx/.htpasswd usuario
   ```

3. **Configurar autenticación en el sitio**:
   - Modificar el archivo de configuración del sitio:
     ```nginx
     location /protected {
         auth_basic "Restricted Content";
         auth_basic_user_file /etc/nginx/.htpasswd;
     }
     ```

4. **Reiniciar el servidor web**:
   ```bash
   sudo systemctl restart nginx
   ```

---

## **Habilitar HTTPS**

1. **Obtener un certificado SSL**:
   - Usar Let's Encrypt para certificados gratuitos:
     ```bash
     sudo apt install certbot python3-certbot-nginx
     sudo certbot --nginx -d example.com -d www.example.com
     ```

2. **Configurar HTTPS en el servidor web**:
   - Asegurarse de que el archivo de configuración incluye:
     ```nginx
     server {
         listen 443 ssl;
         ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
         ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
     }
     ```

3. **Validar y reiniciar**:
   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

---

## **Instalación de la Pila LAMP**

1. **Instalar componentes básicos**:
   - **Apache**:
     ```bash
     sudo apt install apache2
     ```
   - **MariaDB**:
     ```bash
     sudo apt install mariadb-server
     sudo mysql_secure_installation
     ```
   - **PHP**:
     ```bash
     sudo apt install php libapache2-mod-php php-mysql
     ```

2. **Configurar Apache con PHP**:
   - Editar `/etc/apache2/sites-available/000-default.conf`:
     ```apache
     DocumentRoot /var/www/html
     <Directory /var/www/html>
         AllowOverride All
     </Directory>
     ```
   - Reiniciar Apache:
     ```bash
     sudo systemctl restart apache2
     ```

3. **Probar configuración**:
   - Crear un archivo `info.php`:
     ```php
     <?php
     phpinfo();
     ?>
     ```
   - Acceder desde el navegador: `http://<server_ip>/info.php`.

---

## **Dockerización de Servicios**

1. **Instalar Docker y Docker Compose**:
   ```bash
   sudo apt install docker.io
   sudo apt install docker-compose
   ```

2. **Configurar servicios en `docker-compose.yml`**:
   ```yaml
   version: '3.8'
   services:
     web:
       image: php:apache
       volumes:
         - ./html:/var/www/html
       ports:
         - "8080:80"
     db:
       image: mariadb
       environment:
         MYSQL_ROOT_PASSWORD: rootpassword
         MYSQL_DATABASE: exampledb
         MYSQL_USER: exampleuser
         MYSQL_PASSWORD: examplepassword
       ports:
         - "3306:3306"
   ```

3. **Iniciar servicios**:
   ```bash
   docker-compose up -d
   ```

4. **Validar la configuración**:
   - Acceder al sitio en `http://localhost:8080`.
   - Verificar la conexión a la base de datos desde PHP.

---

## **Consideraciones Generales**

- **Seguridad**:
  - Configurar permisos adecuados para evitar accesos no autorizados.
  - Usar autenticación y cifrado HTTPS siempre que sea posible.
- **Pruebas**:
  - Validar configuración tras cada cambio con `nginx -t` o `apachectl configtest`.
- **Automatización**:
  - Utilizar scripts o herramientas como Ansible o Terraform para configurar entornos repetibles.
- **Backups**:
  - Realizar copias de seguridad de configuraciones y bases de datos periódicamente.