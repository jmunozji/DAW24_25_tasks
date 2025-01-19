---
title: 'CH5 - EGM'
---

# Configuración de Servidores: FTP/FTPS/SFTP 

---

## 🛠 1. Comandos Básicos FTP

### 🌐 Conexión al Servidor FTP

```sh
 'ftp' # Establece una conexión con un servidor FTP.
 'open [hostname]' # Establece una conexión con un servidor FTP. 
 'user [nombre de usuario] [contraseña]' # Inicia sesión con el usuario y contraseña proporcionados.
```

### 📂 Operaciones de Archivos y Directorios

```sh
'delete [archivo]' # Elimina un archivo en el servidor FTP.                                           
'rmd [directorio]'  # Elimina un directorio en el servidor FTP.                                        
'rename [nombre original] [nuevo nombre]'   # Renombra un archivo o directorio en el servidor FTP.                            
```

### 📤 Transferencia de Archivos

```sh
'get [archivo remoto] [archivo local]'  # Descarga un archivo desde el servidor FTP al equipo local.
'put [archivo local] [archivo remoto]'  # Sube un archivo desde el equipo local al servidor FTP.
```

### 🔄 Modo Activo y Pasivo

```sh
'PASV'  # Activa el modo pasivo en el servidor FTP.                                         
'PORT'  # Activa el modo activo en el servidor FTP.      
```

## 🛠 2. Configuración Servidor vsFTPd  

### 🔧 Instalación

```bash
sudo apt update
sudo apt install vsftpd -y
```

### 🚀 Iniciar y habilitar el servicio

```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

### ⚙️ Archivo de Configuración

```bash
sudo nano /etc/vsftpd.conf
```


```sh
❌ # No permitimos acceso anónimo
anonymous_enable=NO
local_enable=YES

✍️ # Permitir escritura
write_enable=YES

🌟 # Configuración de permisos
local_umask=022

🛡️ # Chroot para usuarios locales
chroot_local_user=YES
allow_writeable_chroot=YES

🗂️ # Directorio raíz personalizado
user_sub_token=userftp
local_root=/home/userftp/ftp

🗂️ # Otra configuración de directorio raíz
user_sub_token=$USER
local_root=/home/$USER/ftp

👥 # Configuración de lista de usuarios
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```
   
**Reiniciar el servicio:**  

```bash
sudo systemctl restart vsftpd
```

```sh
'sudo usermod --home /var/www userftp'  #🏡 Cambia el directorio home del usuario **userftp** al directorio `/var/www`.                                
'sudo adduser userftp www-data'   #👥 Añade el usuario **userftp** al grupo **www-data** para asignar permisos de grupo.
'echo "userftp' | 'sudo tee -a /etc/vsftpd.userlist'    # ➕ Añade el usuario **userftp** a la lista de usuarios permitidos en **vsFTPd**.  
```

---

## 🛠 3. Configuración de FTPS (FTP Seguro con SSL/TLS)  

### 📄 Generar un certificado SSL

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

### ⚙️ Configurar vsftpd para FTPS  

```bash
sudo nano /etc/vsftpd.conf
```

**Habilitar Cifrado SSL:**

Una vez creado el certificado, podemos habilitarlo con la siguiente configuración:

```sh
# 📂 Configuración de certificados SSL predeterminados
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO

# 🛡️ Activar FTPS con un certificado personalizado
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES

allow_anon_ssl=NO                  # ❌ No permitir acceso anónimo con SSL
force_local_data_ssl=YES           # 🔒 Requerir cifrado de datos locales
force_local_logins_ssl=YES         # 🔐 Requerir cifrado para inicios de sesión
ssl_tlsv1=YES                      # ✅ Habilitar protocolo TLSv1
ssl_sslv2=NO                       # ❌ Deshabilitar SSLv2
ssl_sslv3=NO                       # ❌ Deshabilitar SSLv3
require_ssl_reuse=NO               # 🔄 No requerir reutilización de sesión SSL
ssl_ciphers=HIGH                   # 🔐 Usar cifrados fuertes
```

**Habilitar Modo Pasivo:** 

```sh
# 🔄 Configuración de modo pasivo
pasv_enable=YES                    # Habilitar modo pasivo
pasv_min_port=1027                 # Puerto mínimo para conexiones pasivas
pasv_max_port=1030                 # Puerto máximo para conexiones pasivas
pasv_address=X.X.X.X               # Dirección IP pública del servidor
```

**Reiniciar el Servicio:**

```bash
sudo systemctl restart vsftpd
```

---

## 🛠 4. Conexión SFTP

Podemos tener la configuración por defecto

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.ftps
sudo cp /etc/vsftpd.conf.backup2 /etc/vsftpd.conf
```

Simplemente tenemos que tener un par de claves creadas, con eso hecho ejecutamos el siguiente comando:

```bash
ssh -i clave user@ipsever
```