---
title: 'CH5 - RSN'
---

## Acceso FTP publico ▶️

| Comandos 🛡️ | Explicación 🗨️ |
-------------------- | -----------------------
| `open ftp` | Conectarse a una red pública como anónimo |
| `get constitution.txt` | Obtener archivo de la red |
| `quit` | Salir de la red |

## Instalar y configurar el servidor vsFTPd sin cifrado 🔓

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo apt-get install vsftpd` | Obtener servidor FTPd |
| `sudo adduser userftp` | Creación de usuario FTP |
| `sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup` | Copia de seguridad de vsftpd.conf |
| `sudo adduser userftp www-data` | Añadir el usuario para subir archivos web |

## Configuración de un servidor FTP con cifrado 🔒

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem` | Generar  certificado |

## Servidor FTP dockerizado sin cifrado 📦🔓

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- 
| `docker run  rm  e FTP_USER=userftp  e FTP_PASS=ieselcaminas  e PASV_MIN_PORT=21100  e PASV_MAX_PORT=21110  p 21:21 p 21100-21110:21100-21110  d  name pruebavsftpd  v /home/admin/datosftp:/home/vsftpd fauria/vsftpd` | Dockerización con modo pasivo y craeción de usuario |


## Servidor FTP dockerizado con cifrado 📦🔒
| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `docker run -e FTP_USER=userftp -e FTP_PASS=ieselcaminas -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 -e LOCAL_UMASK=022 -p 21:21 -p 21100-21110:21100-21110 -d --name vsftpdconcifrado -v /home/admin/datosftp:/home/vsftpd mivsftpd` |Dockerización con cifrado |

## Configuraciones de archivos 📜


```c
> Archivo configuración vsftpd.conf (P1)

    //Habilitar usuarios locales
    //Deshabilitar usuarios anónimos
    //Habilitar escritura
    anonymous_enable=NO
    local_enable=YES
    allow_writeable_chroot=YES

    //Crear lista de usuarios para que puedan entrar
    userlist_enable=YES #
    userlist_file=/etc/vsftpd.userlist #
    userlist_deny=NO #
```

```c
> Archivo configuración vsftpd.conf (P1)

    //Habilitar certificado

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

    //Habilitar modo pasivo
    pasv_enable=YES
    pasv_min_port=1027  # Puerto mínimo de conexión pasiva (reemplaza XXXX con un número)
    pasv_max_port=1030  # Puerto máximo de conexión pasiva (reemplaza XXXX con un número)
    pasv_address=X.X.X.X  # Dirección IP pública o accesible desde el cliente (reemplaza X.X.X.X con la dirección IP)

```
