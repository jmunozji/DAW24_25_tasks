---
title: 'CH5 - DGD'
---

# CHEATSHEET 5 SERVICIOS DE RED FTP

## 🎓 BÁSICO FTP

- Entrar al ftp de tu ordenador:

```bash
ftp
```
- Conectarte a una red pública:

```bash
open ftp.rediris.es
```

- Descargar archivo:

```bash
get archivo
```

- Subir archivo:

```bash
put arhivo
```

- Salir:

```bash
quit
```


## 📥 INSTALACIÓN SERVIDOR VSFTP

```bash
sudo apt-get install vsftpd
```

### 📈 INICIAR Y HABILITAR SERVICIO

```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd.service
```

## ⚙️ ARCHIVO DE CONFIGURACIÓN

El archivo de configuración es: **/etc/vsftpd.conf**

### ⚙️ CONFIGURACIONES BÁSICAS

|Configuración|Explicación|
|-------------|-----------|
|**anonymous_enable=NO**|No permite la autenticación anónima en el servidor|
|**local_enable=YES**|Permite acceso a usuarios locales|
|**write_enable=YES**|Habilita la carga de archivos|
|**local_umask**|Indica que permisos tendrán los archivos al subirse al servidor|
|**chroot_local_user=YES**|Restringe a los usuarios a sus directorios de inicio|
|**local_root**|Indica el directorio raiz de los usuarios


## ❌ RESTRICCIÓN DE USUARIOS

Para restringir usuarios hay que crear el siguiente archivo:

**/etc/vsftpd.userlist**

Añadimos los usuarios que queremos permitir el acceso o restringirlo y añadimos estas configuraciones:

```conf
userlist_enable=YES
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO
```

## 📝 CREAR CERTIFICADO SSL

Podemos crear el certificado con el siguiente comando:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

### ✅ HABILITAR CIFRADO SSL

Una vez creado el certificado, podemos habilitarlo con la siguiente configuración:

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
```


## ✅ HABILITAR MODO PASIVO

Para habilitar el modo pasivo también se requiere modificar la configuración añadiendo:

```conf
pasv_enable=YES
pasv_min_port=1027 # Puerto mínimo de conexión
pasv_max_port=1030 # Puerto máximo de conexión
pasv_address=X.X.X.X # Dirección IP pública
```
