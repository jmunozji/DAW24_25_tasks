---
title: 'CH5 - DMP'
---

# 📝 UD5 - Servicios de red FTP - Cheatsheet

> David Maratrat Pons

## 🧑‍💻 Comándos básicos de FTP

| Comando               | Descripción                        |
|-----------------------|------------------------------------|
| `ftp`                 | `Entra en modo FTP`                |
| `open <servidor FTP>` | `Se conecta a un servidor FTP`     |
| `get <archivo>`       | `Descarga un archivo del servidor` |
| `put <archivo`        | `Sube un archivo al servidor`      |
| `quit`                | `Salir del modo FTP`               |

---

## 📥 Instalación del servidor FTP

| Comando                                 | Descripción                                    |
|-----------------------------------------|------------------------------------------------|
| `sudo apt-get install vsftpd`           | `Descarga e instala el servidor vsftpd`        |
| `sudo systemctl start vsftpd.service`   | `Arranca el servicio`                          |
| `sudo systemctl restart vsftpd.service` | `Reinicia el servicio`                         |
| `sudo systemctl stop vsftpd.service`    | `Para el servicio`                             |
| `sudo systemctl enable vsftpd.service`  | `Habilita el arranque automático del servidor` |

---

## ⚙️ Configuración del servidor FTP

| Configuración                | Explicación                            |
|------------------------------|----------------------------------------|
| `sudo nano /etc/vsftpd.conf` | `Modifica el archivo de configuración` |

## 📄 vsftpd.conf básico:
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask
chroot_local_user=YES
local_root
```

## 📄 vsftpd.conf pasivo:

```
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030
pasv_address=<server IP>
```

## ❌ Acceso de usuarios

| Configuración                    | Explicación                                      |
|----------------------------------|--------------------------------------------------|
| `sudo nano /etc/vsftpd.userlist` | `Modifica el acceso de usuarios en este archivo` |

## 📄 vsftpd.conf usuarios:

```
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

## 📝 SFTP

| Configuración                                                                                                                    | Explicación               |
|----------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem`  | `Crea el certificado SSL` |

## 📄 vsftpd.conf seguro:

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