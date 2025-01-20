---
title: 'CH5 - RRL'
---

# SERVICIOS DE RED FTP

## COMÁNDOS BÁSICOS

### FTP

```bash
ftp
```

Entra en el modo **FTP**.

### OPEN

```bash
open 'servidor_FTP'
```

Se conecta a un servidor **FTP**.

### GET

```bash
get 'archivo'
```

Descarga un archivo del servidor.

### PUT

```bash
put 'archivo'
```

### QUIT

Sube un archivo al servidor.

```bash
quit
```

Salir del modo FTP.

## INSTALACIÓN DEL SERVIDOR FTP

```bash
sudo apt install vsftpd
```

Descarga el servidor vsftpd.

```bash
sudo systemctl start vsftpd.service
```

Arranca el servicio vsftpd.

```bash
sudo systemctl restart vsftpd.service
```

Reinicia el servicio vsftpd.

```bash
sudo systemctl stop vsftpd.service
```

Para el servicio vsftpd.

```bash
sudo systemctl enable vsftpd.service
```

Habilita el arranque automático del servidor vsftpd.

## Configuración del servidor FTP

```bash
sudo nano /etc/vsftpd.conf
```

Modficar el archivo de configuración.

```bash
anonymous_enable=NO
local_enable=YES
```

Acceso a usuarios locales y no permite el al acceso anónimo.

```bash
write_enable=YES
```

Permitir escritura.

```bash
local_umask=022
```

Configuración de permisos.

```bash
chroot_local_user=YES
```

Restringe el usuario local a su directorio de inicio.

```bash
allow_writeable_chroot=YES
```

Permitir que el directorio raíz al que nos conectamos sí pueda ser escribible.

```bash
user_sub_token=userftp
local_root=/home/userftp/ftp
```

Directorio raíz personalizado.

```bash
sudo nano /etc/vsftpd.userlist
```

```bash
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

Acceso de usuarios.

```bash
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030
pasv_address='server IP'
```

Activar el modo pasivo.

## SFTP

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

Crea el certificado SSL.

## VSFTPD SEGURO

```bash
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
