---
title: 'CH6 - ALS'
---

# 📌 MEGA CHEATSHEET - SERVICIOS DE RED FTP, FTPS Y SFTP

## 🚀 COMANDOS BÁSICOS FTP

| Comando | Descripción |
|---------|------------|
| `ftp` | Iniciar cliente FTP |
| `open [hostname]` | Conectar a un servidor FTP |
| `user [usuario] [contraseña]` | Iniciar sesión en FTP |
| `get [archivo]` | Descargar un archivo |
| `put [archivo]` | Subir un archivo |
| `delete [archivo]` | Eliminar un archivo en el servidor |
| `rmd [directorio]` | Eliminar un directorio en el servidor |
| `rename [nombre_original] [nuevo_nombre]` | Renombrar archivos |
| `quit` | Salir del cliente FTP |

## 🛠️ INSTALACIÓN Y CONFIGURACIÓN DEL SERVIDOR FTP (vsftpd)

### 📥 Instalación
```bash
sudo apt update
sudo apt install vsftpd -y
```

### 🚀 Iniciar y Habilitar el Servicio
```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

### 👤 Crear Usuario FTP
```bash
sudo adduser ftpuser
sudo passwd ftpuser
sudo mkdir -p /home/ftpuser/ftp/upload
sudo chown nobody:nogroup /home/ftpuser/ftp
sudo chmod a-w /home/ftpuser/ftp
sudo chown ftpuser:ftpuser /home/ftpuser/ftp/upload
```

### ⚙️ Configuración Básica (Editar `/etc/vsftpd.conf`)
```conf
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
chroot_local_user=YES
allow_writeable_chroot=YES
user_sub_token=$USER
local_root=/home/$USER/ftp
pasv_enable=YES
pasv_min_port=1024
pasv_max_port=1048
```
Reiniciar el servicio:
```bash
sudo systemctl restart vsftpd
```

## 🔒 CONFIGURACIÓN DE FTPS (FTP SEGURO CON SSL/TLS)

### 🛠 Generar un Certificado SSL
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

### 📄 Configurar vsftpd para FTPS (Editar `/etc/vsftpd.conf`)
```conf
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
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030
pasv_address=X.X.X.X
```
Reiniciar el servicio:
```bash
sudo systemctl restart vsftpd
```

## 🔑 CONFIGURACIÓN DE SFTP (FTP SEGURO SOBRE SSH)

### ✅ Verificar que SSH está Activo
```bash
sudo systemctl status ssh
```

### 👤 Crear Usuario para SFTP
```bash
sudo adduser sftpuser
sudo passwd sftpuser
```

### 📁 Configurar Directorio de SFTP
```bash
sudo mkdir -p /home/sftpuser/sftp/upload
sudo chown root:root /home/sftpuser/sftp
sudo chmod 755 /home/sftpuser/sftp
sudo chown sftpuser:sftpuser /home/sftpuser/sftp/upload
```

### ✏️ Editar Configuración de SSH (`/etc/ssh/sshd_config`)
```conf
Match User sftpuser
    ChrootDirectory /home/sftpuser/sftp
    ForceCommand internal-sftp
    AllowTcpForwarding no
```
Reiniciar SSH:
```bash
sudo systemctl restart ssh
```

## 🔗 PROBAR LAS CONEXIONES

### 📡 Conexión FTP
```bash
ftp <DIRECCIÓN_IP>
```

### 🔒 Conexión FTPS (Usando FileZilla)
- Protocolo: **FTPS Explícito**
- Puerto: **21**
- Usuario: **ftpuser**

### 🔑 Conexión SFTP
```bash
sftp sftpuser@<DIRECCIÓN_IP>
```
