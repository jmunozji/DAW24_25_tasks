---
title: 'CH5 - RPE'
---

# **📄 Cheatsheet de Servicios de Red FTP**

## **🔧 Comandos básicos FTP**

| **Comando**       | **Descripción**                                  |
|-------------------|--------------------------------------------------|
| `ftp`             | Entrar en el cliente FTP.                        |
| `open ftp://<IP>` | Conectarse a un servidor FTP.                    |
| `get <archivo>`   | Descargar un archivo desde un servidor FTP.      |
| `quit`            | Salir del cliente FTP.                           |

---

### **⚙️ Instalación de un servidor vsFTPd**

1. Actualizar el sistema e instalar vsFTPd:

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install vsftpd
```

---

#### **🛠️ Archivos de configuración**

Editar el archivo `/etc/vsftpd.conf` y añadir las siguientes líneas:

```code
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
chroot_local_user=YES
allow_writeable_chroot=YES
user_sub_token=<usuario>
local_root=<directorio>
```

---

#### **🚫 Restricción de usuarios FTP**

Para restringir el acceso a FTP, agrega estos parámetros al archivo `/etc/vsftpd.conf`:

```code
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

---

### **🔐 Configuración de FTP con cifrado**

#### **🛡️ Crear un certificado SSL**

Generar un certificado SSL con el siguiente comando:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

#### **⚡ Habilitar el cifrado**

Reemplazar las siguientes líneas en `/etc/vsftpd.conf`:

De:

```code
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO
```

Por:

```code
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

---

#### **📡 Habilitar modo pasivo**

Añadir las siguientes líneas en `/etc/vsftpd.conf`:

```code
pasv_enable=YES
pasv_min_port=1027  # Puerto mínimo de conexión pasiva.
pasv_max_port=1030  # Puerto máximo de conexión pasiva.
pasv_address=X.X.X.X  # Dirección IP pública del servidor.
```

---

### **🌐 Configuración de SFTP**

Generar un par de claves SSH y conectarse con el siguiente comando:

```bash
ssh -i clave user2@ipserver
```

