

--- 
title: 'CH5 - AEM'
---
# Cheat Sheet - Unidad 5

## Instalación y Configuración del Servidor vsFTPd (sin cifrado)

### Instalación del servidor

```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install vsftpd

```

### Verificación de la instalación

```bash
cat /etc/passwd | grep ftp
cat /etc/group | grep ftp
systemctl status vsftpd
ps -ef | grep vsftpd

```

----------

## Archivos y Directorios Importantes

-   **/etc/init.d/vsftpd** - Script de inicio para gestionar el servicio.
-   **/usr/sbin/vsftpd** - Binario principal del servidor.
-   **/etc/vsftpd.conf** - Archivo de configuración principal.
-   **/srv/ftp** - Directorio por defecto para archivos.
-   **/etc/ftpusers** - Lista de usuarios denegados.
-   **/etc/vsftpd.user_list** - Lista de usuarios permitidos.
-   **/etc/vsftpd.chroot_list** - Lista de usuarios con acceso restringido.
-   **/var/log/vsftpd.log** - Archivo de registros del servidor.

----------

## Comandos del servicio vsFTPd

```bash
sudo systemctl start vsftpd   # Iniciar el servicio
sudo systemctl stop vsftpd    # Detener el servicio
sudo systemctl restart vsftpd # Reiniciar el servicio
sudo systemctl reload vsftpd  # Recargar la configuración
sudo systemctl status vsftpd  # Ver el estado del servicio

```

----------

## Configuración del servidor

### Copia de seguridad de configuración

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup

```

### Editar configuración

```bash
sudo nano /etc/vsftpd.conf

```

### Configuraciones clave

1.  **Acceso solo a usuarios locales**
    
    ```ini
    anonymous_enable=NO
    local_enable=YES
    
    ```
    
2.  **Habilitar carga de archivos**
    
    ```ini
    write_enable=YES
    
    ```
    
3.  **Permisos de archivos subidos**
    
    ```ini
    local_umask=022
    
    ```
    
4.  **Cárcel de chroot para usuarios locales**
    
    ```ini
    chroot_local_user=YES
    allow_writeable_chroot=YES
    
    ```
    
5.  **Directorio de acceso para usuarios**
    
    ```ini
    user_sub_token=$USER
    local_root=/home/$USER/ftp
    
    ```
    
6.  **Restricción de usuarios**
    
    ```ini
    userlist_enable=YES
    userlist_file=/etc/vsftpd.userlist
    userlist_deny=NO
    
    ```
    
    Agregar usuario permitido:
    
    ```bash
    echo "userftp" | sudo tee -a /etc/vsftpd.userlist
    
    ```
    

----------

## Creación de usuario para pruebas

```bash
sudo adduser userftp

```

Configuración del directorio para chroot:

```bash
mkdir -p /home/userftp/ftp/upload
chmod a-w /home/userftp/ftp
chmod 755 /home/userftp/ftp/upload

```

----------

## Acceso FTP

Conectarse al servidor FTP:

```bash
ftp <IP_DEL_SERVIDOR>

```

Subir y descargar archivos:

```ftp
put archivo.txt
get archivo.txt
bye

```

### Comprobaciones finales

1.  Solo usuarios locales pueden conectarse.
2.  La carga de archivos está habilitada.
3.  Los archivos se suben con permisos umask 022.
4.  Chroot habilitado a `/home/$USER/ftp`.
5.  Solo `userftp` puede conectarse.

Crear archivo de prueba:

```bash
echo "esto es una prueba con vsftpd" | sudo tee /home/userftp/ftp/upload/pruebaftp.txt

```

----------

## Resolución de Problemas

-   Ver registros de actividad:
    
    ```bash
    cat /var/log/vsftpd.log
    
    ```
    
-   Verificar firewall:
    
    ```bash
    sudo ufw status
    sudo ufw allow 21/tcp
    
    ```
    
-   Comprobación de puertos abiertos:
    
    ```bash
    netstat -tuln | grep :21
    
    ```
    

----------

## Configuración de vsFTPd con cifrado (FTPS)

### Generar un certificado autofirmado

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem

```

Verificar la creación del certificado:

```bash
sudo ls -l /etc/ssl/private

```

### Configuración del servidor para FTPS

Hacer una copia de seguridad del archivo de configuración:

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup2

```

Editar configuración:

```bash
sudo nano /etc/vsftpd.conf

```

Añadir las siguientes líneas:

```ini
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

Habilitar el modo pasivo:

```ini
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030
pasv_address=X.X.X.X

```

Reiniciar el servicio:

```bash
sudo systemctl restart vsftpd

```

----------

## Configuración de SFTP

Para habilitar SFTP no es necesario configurar vsftpd, solo asegurarse de que SSH está configurado correctamente.

Comprobar el estado del servicio SSH:

```bash
sudo systemctl status ssh

```

Probar la conexión SFTP:

```bash
sftp user2@ipserver

```

Conexión mediante clave SSH:

```bash
ssh -i clave user2@ipserver

```

----------

## Dockerización del servidor vsFTPd

### Ejecutar un contenedor básico

```
docker run --rm -p 21:21 -p 20:20 -d --name pruebavsftpd fauria/vsftpd
```

Verificar credenciales de acceso:

```
docker logs pruebavsftpd
```

### Montar directorio de datos en el host

```
mkdir datosftp
docker run --rm -p 21:21 -p 20:20 -d --name pruebavsftpd -v /home/admin/datosftp:/home/vsftpd fauria/vsftpd
```

### Contenedor con usuario y modo pasivo

```
docker run -e FTP_USER=userftp -e FTP_PASS=ieselcaminas -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 -p 21:21 -p 21100-21110:21100-21110 -d --name pruebavsftpd -v /home/admin/datosftp:/home/vsftpd fauria/vsftpd
```

### Configurar permisos de archivos

```
docker run -e FTP_USER=userftp -e FTP_PASS=ieselcaminas -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 -e LOCAL_UMASK=022 -p 21:21 -p 21100-21110:21100-21110 -d --name vsftpdsincifrado -v /home/admin/datosftp:/home/vsftpd fauria/vsftpd
```

----------
