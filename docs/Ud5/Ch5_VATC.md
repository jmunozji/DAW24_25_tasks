---

title: 'CH5 - VATC'

---

# �� Servicios de Red FTP

> ✍️ **Victor Alexandru Teleanu Covaci**

## ��️ **1. Comandos Básicos FTP**

### �� **Conexión al Servidor FTP**

| **�� Comando**                        | **�� Descripción**                                                  |
|----------------------------------------|---------------------------------------------------------------------|
| `open` o `open [hostname]`             | 🔗 Establece una conexión con un servidor FTP.                     |
| `user [nombre de usuario] [contraseña]`| 👤 Inicia sesión en el servidor FTP.                               |
| `ftp <DIRECCIÓN_IP>`                   | 🔗 Conecta al servidor FTP mediante la dirección IP proporcionada. |
| `sftp sftpuser@<DIRECCIÓN_IP>`         | 🔑 Establece una conexión SFTP con el servidor.                    |

### �� **Transferencia de Archivos**

| **�� Comando**                        | **�� Descripción**                                         |
|---------------------------------------|---------------------------------------------------------------|
| `get [archivo remoto] [archivo local]`| ⬇️ Descarga un archivo desde el servidor FTP al equipo local. |
| `put [archivo local] [archivo remoto]`| ⬆️ Sube un archivo desde el equipo local al servidor FTP.     |

### �� **Modo Activo y Pasivo**

| **�� Comando**                | **�� Descripción**                                                                      |
|-------------------------------|--------------------------------------------------------------------------------------------|
| `PASV`                        | 🔒 El cliente solicita al servidor FTP cambiar a modo pasivo y abrir un puerto para datos. |
| `PORT`                        | 🔓 En modo activo, el cliente usa PORT para indicar el puerto de la conexión de datos.    |

### �� **Ayuda y salida**

| **�� Comando**                | **�� Descripción**                             |
|-------------------------------|---------------------------------------------------|
| `help`                        | ❔ Muestra la lista de comandos FTP disponibles. |
| `quit`                        | 🚪 Cierra la sesión FTP y sale del cliente.      |

---

## �� **2. Instalación y Configuración de vsFTPd**

### **✅ Instalación en Debian**

1. Actualiza los repositorios e instala vsFTPd:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install vsftpd
```

2. `systemctl status vsftpd` verifica que el servicio está activo.

### **�� Crear un usuario para FTP**

```bash
sudo adduser ftpuser
```

### **��️ Configuración de vsFTPD**

1. Crea un copia de seguridad del archivo de configuración con `sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup`.

2. Modifica el archivo con `sudo nano /etc/vsftpd.conf`.

3. **Fichero de configuración:**
```bash
# 🚫 Deshabilitar el acceso anónimo
anonymous_enable=NO

# 👥 Habilitar el acceso local
local_enable=YES

# ✏️ Permitir escritura
write_enable=YES

# 🔒 Configuración de chroot para usuarios locales
chroot_local_user=YES
allow_writeable_chroot=YES

# 🗂️ Establecer la ruta local para el usuario
user_sub_token=userftp
local_root=/home/userftp/ftp

# 🌍 Configuración dinámica para el usuario actual
user_sub_token=$USER
local_root=/home/$USER/ftp

# 📝 Habilitar lista de usuarios
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

4. Reinicia el servicio con `sudo systemctl restart vsftpd`.

## �� **3. Configuración de FTPS (FTP Seguro con SSL/TLS)**

1. Genera un certificado SSL:
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

2. Configurar vsftpd para FTPS con `sudo nano /etc/vsftpd.conf`.

3. Agregar o modificar las siguientes lineas:
```bash
# 🔒 Certificados SSL para la conexión segura
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem

# ⚙️ Habilitar SSL
ssl_enable=YES

# 🚫 Deshabilitar acceso anónimo en SSL
allow_anon_ssl=NO

# 🔐 Forzar el uso de SSL para la transferencia de datos y el inicio de sesión
force_local_data_ssl=YES
force_local_logins_ssl=YES

# 🔄 Especificar protocolos SSL/TLS permitidos
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO

# 🚫 Requerir reutilización de sesiones SSL
require_ssl_reuse=NO

# 💎 Establecer cifrados SSL/TLS
ssl_ciphers=HIGH

# 🌐 Habilitar modo pasivo y configuración de puertos
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030

# 🖥️ Dirección IP para conexiones pasivas
pasv_address=<DIRECCIÓN_IP>
dress=<DIRECCIÓN_IP>
```

4. Reinicia el servicio con `sudo systemctl restart vsftpd`.

## �� **4. Conexión SFTPS**

```bash
ssh -i clave user2@ipserver
```
