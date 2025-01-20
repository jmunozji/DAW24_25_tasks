---
title: 'CH5 - EV'
---

## **Acceso a un servidor FTP público ftp.rediris.es**

- Entrar en el servidor ftp del ordenador
: `ftp`
- Visualizar los comandos ftp disponibles
: `help`
- Conectar con la red pública `ftp.rediris.es`
: `open ftp.rediris.es`
- Acceder a la carpeta `/debian/doc`, listar el contenido de archivos y directorios con `ls` y descargar el archivo constitution.txt con `get constitution.txt`
- Salir del servidor ftp con `quit` y listar los archivos de la máquina para ver si se ha descargado correctamente

### Acceso desde cliente gráfico

- Crear `Nuevo sitio` sin cifrado
- Servidor
: `ftp.rediris.es`
- Cifrado
: `Usar sólo FTP plano`
- Modo de acceso
: `anónimo`
- Descargar el archivo en `/debian/doc/constitution.txt` y finalizar la conexión con el icono de desconectar

## **Instalar y Configurar el servidor vsFTPd sin cifrado.**

### Crear una instancia en AWS

- Añadir una regla de entrada para permitir el tráfico en el puerto FTP que se necesita
- Para FTP no cifrado `puerto 21`
- Para FTPS o SFTP con cifrado `puerto 22`

### Instalación del servidor vsFTPd

- Actualizar repositorios de Ubuntu e instalar el servidor vsFTPd
```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install vsftpd
```
Comprobar que se han creado el usuario y grupo `ftp` en `/etc/passwd` y `/etc/group`
- Comprobar que el servicio está en marcha
: `systemctl status vsftpd`
- También se puede comprobar que el servicio vsftpd está funcionando
: `ps -ef | grep vsftpd`

### Configurar usuario de pruebas

- Crear usuario userftp
: `sudo adduser userftp`

### Configuración del servidor vsFTPd

- Guardar una copia del archivo de configuración
: `sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup`
- Abrir el archivo de configuración
: `sudo nano /etc/vsftpd.conf`
- Permitir el acceso FTP a usuarios locales y deshabilitar cualquier acceso anónimo. Comprobar estas dos líneas en el archivo `/etc/vsftpd.conf`
```bash
  anonymous_enable=NO #
  local_enable=YES #
```
- Guardar el archivo y reiniciar el servicio vsftpd
: `sudo systemctl restart vsftpd`
- Asegurarse de que el servicio vsftpd está en su estado de ejecución
: `sudo systemctl status vsftpd`
- Probar a conectarse con el usuario `userftp` con el cliente FTP
- Descomentar la línea `write_enable=YES` para habilitar la carga de archivos
Descomentar la línea `#local_umask=022` del archivo de configuración, reiniciar el servicio, subir un fichero a `/home/userftp` y comprobar sus permisos
- Cambiar la configuración con la propiedad `chroot_local_user=YES` para que los usuarios estén restringidos a sus directorios de inicio de forma predeterminada

### Enjaular al usuario en una carpeta distinta, por ejemplo /var/www

- Instalar apache
- Cambiar el directorio home del usuario
: `sudo usermod --home /var/www userftp`
- Comprobar con
```bash
su userftp
cd
pwd
```
- Añadir el usuario al grupo de apache o www-data dependiendo del sistema operativo o el usuario apache configurado
: `sudo adduser userftp www-data`

### Restricción de usuarios

- Agregar las siguientes líneas y especificar qué usuarios deben poder usar FTP y agregar sus nombres de usuario en el archivo `/etc/vsftpd.userlist`
```bash
userlist_enable=YES #
userlist_file=/etc/vsftpd.userlist #
userlist_deny=NO #
```
- Agregar el nuevo usuario `userftp` a la lista de usuario FTP permitidos
: `echo "userftp" | sudo tee -a /etc/vsftpd.userlist`
- Guardar el archivo, reiniciar el servidor e intentar conectarse con `userftp`

### Comprobación del acceso FTP

- Comrobar que no se puede iniciar sesión como usuario anónimo en el servidor FTP
- Conectarse como `userftp`
- Cambiar al directorio de carga y usar el comando get para transferir el archivo de prueba a la máquina local
: `get pruebaftp.txt`
- Subir el archivo con un nuevo nombre usando el comando put para probar los permisos de escritura del arhcivo
: `put pruebaftp.txt pruebaftp2.txt`
- Cerrar la conexión
: `ftp> bye`
- Comprobar con `ls` que el archivo se ha descargado correctamente en la máquina local

## **Configuración de servidor FTP con Cifrado**

- Abrir instancia anterior de AWS
- Comprobar el estado del servicio
: `sudo systemctl status vsftpd`
- En caso de que no esté iniciado
```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd.service
```

### FTPS (File Transfer Protocol Secure)

- Generar un certificado autofirmado con OpenSSL
: `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem`
- Comprobar que se ha generado correctamente
: `sudo ls -l /etc/ssl/private`

### Configurar el servidor vsftpd

- Guardar una copia del archivo de configuración
: `sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup2`
- Modificar el archivo de configuración
- Eliminar o comentar las siguientes líneas
```bash
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO
```
- Añadir las siguientes líneas
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
- Definir los puertos que puede abrir el servidor para el canal de datos en la conexión pasiva en el fichero `/etc/vsftpd.conf` con las siguientes órdenes
```bash
pasv_enable=YES
pasv_min_port=1027  # Puerto mínimo de conexión pasiva (reemplaza XXXX con un número)
pasv_max_port=1030  # Puerto máximo de conexión pasiva (reemplaza XXXX con un número)
pasv_address=X.X.X.X  # Dirección IP pública o accesible desde el cliente (reemplaza X.X.X.X con la dirección IP)
```
- Reiniciar el servicio
: `sudo systemctl restart --now vsftpd`

### Comprobar la Conexión FTP al servidor vsftpd

- En el terminal, escribir la siguiente línea para iniciar una sesión FTP reemplazando el nombre_de_host_ftp con la dirección IP PÚBLICA o el nombre de dominio del servidor FTP al que se desea conectar
: `ftp nombre_de_host_ftp`
- Si la conexión es exitosa, se verá un mensaje similar a este
```bash
Connected to nombre_de_host_ftp.
220 (nombre_del_servidor_ftp) FTP server ready
Name (nombre_de_host_ftp:tu_nombre_de_usuario_ftp):
```
- Acceder a Filezilla y crear una conexión para usarla más adelante
```bash
- Protocolo: FTP - Protocolo de Transferencia de Archivos
- Servidor: IP server
- Cifrado: Requiere FTP explícito sobre TLS
```

### SFTP (SSH File Transfer Protocol)

- Guardar el fichero de configuración con ftps activado y recuperar el fichero de configuración antes de configurar FTPS
```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.ftps
sudo cp /etc/vsftpd.conf.backup2 /etc/vsftpd.conf
```
- Crear un par de claves pública y privada para el usuario `user2` y configurar el servidor ssh en la máquina virtual en AWS para que el usuario pueda conectarse con ssh usando su clave privada
- Probar que funciona
: `ssh -i clave user2@ipserver`

### Conexión en modo comando

`sftp -i <archivo .pem> usuario@ipserver`

### Conexión en modo gráfico

- En Filezilla, seleccionar el puerto 22 y tener el arhivo de clave privada en la ubicación donde el siustema espera encontrarlo
- Si el fichero con la clave privada de `user2` está en otro sitio se puede crear una nueva conexión así
```bash
Archivo de claves: ruta completa al fichero
```
