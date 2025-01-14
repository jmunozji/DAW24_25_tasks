---
title: 'CH5 - PPE'
---

# **📄 Cheatsheet UD5 - Servicios de red FTP**

> Este documento resume los comandos y ficheros de configuración para la creación y gestión de servicios de red **FTP** en sistemas Ubuntu, correspondientes a la **Unidad 5** del curso de **Despliegue de Aplicaciones**.

---

## 🛠️ Comandos Básicos FTP

### 🌐 Conexión al Servidor FTP

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `ftp`                         | 🌟 Establece una conexión con un servidor FTP.                                       |
| `open [hostname]`             | 🔗 Establece una conexión con un servidor FTP.                                       |
| `user [nombre de usuario] [contraseña]` | 👤 Inicia sesión con el usuario y contraseña proporcionados.                        |



### 📂 Operaciones de Archivos y Directorios

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `delete [archivo]`            | 🗑️ Elimina un archivo en el servidor FTP.                                           |
| `rmd [directorio]`            | 🗂️ Elimina un directorio en el servidor FTP.                                        |
| `rename [nombre original] [nuevo nombre]` | ✏️ Renombra un archivo o directorio en el servidor FTP.                            |



### 📤 Transferencia de Archivos

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `get [archivo remoto] [archivo local]`  | ⬇️ Descarga un archivo desde el servidor FTP al equipo local.                     |
| `put [archivo local] [archivo remoto]`  | ⬆️ Sube un archivo desde el equipo local al servidor FTP.                         |



### 🔄 Modo Activo y Pasivo

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `PASV`                        | 🔒 Activa el modo pasivo en el servidor FTP.                                         |
| `PORT`                        | 🔓 Activa el modo activo en el servidor FTP.                                         |



### 🔍 Utilidades

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `help`                        | ❔ Muestra la lista de comandos disponibles en FTP.                                  |
| `quit`                        | 🚪 Cierra la conexión con el servidor FTP.                                           |

---

## 🖥️ Configuración del servidor **vsFTPd**

### 🔧 Instalación

| **🔧 Comando**                | **📝 Funcionalidad**                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------|
| `sudo apt-get install vsftpd` | 🛠️ Instala el servidor FTP **vsFTPd** en el sistema Ubuntu.                          |


### ⚙️ Fichero de configuración

```sh
# ❌ No permitimos acceso anónimo
anonymous_enable=NO
local_enable=YES

# ✍️ Permitir escritura
write_enable=YES

# 🌟 Configuración de permisos
local_umask=022

# 🛡️ Chroot para usuarios locales
chroot_local_user=YES
allow_writeable_chroot=YES

# 🗂️ Directorio raíz personalizado
user_sub_token=userftp
local_root=/home/userftp/ftp

# 🗂️ Otra configuración de directorio raíz
user_sub_token=$USER
local_root=/home/$USER/ftp

# 👥 Configuración de lista de usuarios
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

### 🧰 Comandos útiles

| **🔧 Comando**                                      | **📝 Funcionalidad**                                                                                         |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo usermod --home /var/www userftp`              | 🏡 Cambia el directorio home del usuario **userftp** al directorio `/var/www`.                                |
| `sudo adduser userftp www-data`                     | 👥 Añade el usuario **userftp** al grupo **www-data** para asignar permisos de grupo.                         |
| `echo "userftp" | sudo tee -a /etc/vsftpd.userlist`  | ➕ Añade el usuario **userftp** a la lista de usuarios permitidos en **vsFTPd**.                              |

---

## 🔐 Configuración con Cifrado

### 🔑 Conexión FTPS

| **🔧 Comando**                                      | **📝 Funcionalidad**                                                                                         |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem` | 🔒 Genera un certificado SSL para habilitar la conexión FTPS.                                                |

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

```sh
# 🔄 Configuración de modo pasivo
pasv_enable=YES                    # Habilitar modo pasivo
pasv_min_port=1027                 # Puerto mínimo para conexiones pasivas
pasv_max_port=1030                 # Puerto máximo para conexiones pasivas
pasv_address=X.X.X.X               # Dirección IP pública del servidor
```

### 🔐 Conexión SFTP

| **🔧 Fichero de configuración**                    | **📝 Funcionalidad**                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sftp -i clave user2@ipserver`                     | 🔑 Conecta mediante SFTP utilizando una clave privada para autenticar al usuario user2 en el servidor.                                                                   |