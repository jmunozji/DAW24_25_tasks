---
title: 'Ch5 - ACF'
---

# 📡 FTP

### 1. 🔑 Comandos de acceso e información 

| Comando | Explicación |
|----------|----------|
| `ftp` | 🖧 Comando para acceder al servidor ftp de nuestro equipo. |
| `help` | ❔ Comando para visualizar los comandos ftp. |
| `ftp nombre_de_host_ftp` | 🌐 Comando para conectarte a un servidor ftp. |
| `ftp> bye` | 🚪 Comando para cerrar la conexión. |
| `open ftp.rediris.es` | 🔐 Comando para conectar con una red pública. |
| `get constitution.txt` | ⬇️ Comando para descargar un archivo. |
| `quit` | 🚫 Comando para salir del servidor ftp. |

### 2. ⚙️ Comandos de instalación y configuración de vsFTPd

| Comando | Explicación |
|----------|----------|
| `sudo apt-get install vsftpd` | 🛠️ Comando para instalar el servidor vsFTPd |

🔐 **Cárcel de Chroot para los usuarios locales:**
```
chroot_local_user=YES
```
```
user_sub_token=$USER
local_root=/home/$USER/ftp
```
🚫**Restricción de usuarios:**
```
userlist_enable=YES #
userlist_file=/etc/vsftpd.userlist #
userlist_deny=NO #
```
### 3. 🔒 Comandos de configuración de servidor FTP con Cifrado

| Comando | Explicación |
|----------|----------|
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem` | 🔏 Comando para generar un certificado autofirmado con OpenSSL|
| `sudo ls -l /etc/ssl/private` | 📄 Comando para comprobar el certificado |

📝 **Habilitar el cifrado SSL**:

`sudo nano /etc/vsftpd.conf`
```
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES

allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
```

📝 Habilitar el modo pasivo:
```
pasv_enable=YES
pasv_min_port=1027  # Puerto mínimo de conexión pasiva (reemplaza XXXX con un número)
pasv_max_port=1030  # Puerto máximo de conexión pasiva (reemplaza XXXX con un número)
pasv_address=X.X.X.X  # Dirección IP pública o accesible desde el cliente (reemplaza X.X.X.X con la dirección IP)
```
