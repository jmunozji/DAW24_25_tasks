---
title: 'CH5 - MMC'
---

# CheatSheet FTP

## Comandos Básicos FTP 🔍

| ftp | Entrar en el servidor ftp |
| --- | --- |
| open ftp.rediris.es | Conexión a una red  |
| get <nombre_archivo> | Descargar archivos  |
| quit | Salir del servidor ftp |

## vsFTPd sin cifrado ☁️

| sudo apt-get install vsftpd | Instalación vsFTPd |
| --- | --- |
| /etc/bind/named.conf | Archivo de configuración de Bind |
|   write_enable=YES | Línea, en /etc/vsftpd.conf, necesaria para poder cargar archivos. |
| chroot_local_user=YES | Línea, en /etc/vsftpd.conf, para habilitar las restricciones a usuarios locales. |
| userlist_enable=YES #
userlist_file=/etc/vsftpd.userlist #
userlist_deny=NO # | Permiso que solo ciertos usuarios puedan iniciar sesión en el servidor FTP. Luego hay que agregar los nombre de usuarios a /etc/vsftpd.userlist. |

## vsFTPd con cifrado (FTPS) *⛈️*

| sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem | Creación de certificado SSL con OpenSSL. |
| --- | --- |
| Abrimos /etc/vsftpd.conf y eliminamos las siguientes líneas:

rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO | Las cambiamos por estas líneas:

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
ssl_ciphers=HIGH |
| pasv_enable=YES
pasv_min_port=1027  # Puerto mínimo de conexión pasiva (reemplaza XXXX con un número)
pasv_max_port=1030  # Puerto máximo de conexión pasiva (reemplaza XXXX con un número)
pasv_address=X.X.X.X  # Dirección IP pública o accesible desde el cliente (reemplaza X.X.X.X con la dirección IP) | Añadimos estas líneas en /etc/vsftpd.conf para habilitar el modo pasivo |
