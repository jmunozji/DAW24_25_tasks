---
title: 'Ch5 - MIP'
---

# Practica 1 - Acceso a servidor FTP

## **Acceso Modo Comando**

1. **Abrir terminal y ejecutar:**
   ```bash
    ftp help
2. **Conectar al servidor:**
   ```bash
    open ftp.rediris.es
3. **Navegar y descargar archivo:**
   ```bash
    cd /debian/doc
    get constitution.txt
4. **Verificar y salir:**
   ```bash
    quit
## **Acceso con FileZilla**

1. **Conectar sin cifrado:**
- **Servidor:** ftp.rediris.es
- **Usuario:** anonymous
- **Cifrado:** Ninguno

2. **Descargar archivo:**
- Navegar a `/debian/doc`, seleccionar `constitution.txt` y descargarlo.

3. **Desconectar:**
- Clicar en el icono de desconectar.

# Práctica 2 - Instalar y Configurar el servidor vsFTPd sin cifrado

## Introducción

En esta práctica instalaremos y configuraremos el servidor FTP vsFTPd en una máquina virtual Debian, sin cifrado (FTP inseguro, solo para fines didácticos).

## Instalación y Configuración

### Paso 1: Instalación del servidor vsFTPd
1. Actualizamos y luego instalamos vsFTPd:
   ```bash
   sudo apt-get update && sudo apt-get upgrade
   sudo apt-get install vsftpd
2. Verificamos si el servicio está en ejecución:
systemctl status vsftpd
### Paso 2: Crear un Usuario de Prueba
1. Creamos un usuario para pruebas, llamado userftp:
   ```bash
    sudo adduser userftp
### Paso 3: Configuración del servidor vsFTPd
1. Hacemos una copia de seguridad del archivo de configuración:
   ```bash
    sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup
2. Editamos el archivo de configuración:

   ```bash
    sudo nano /etc/vsftpd.conf
Configuraciones clave:

- anonymous_enable=NO: Deshabilitar acceso anónimo.
- local_enable=YES: Habilitar acceso a usuarios locales.

3. Reiniciamos el servicio:

   ```bash
    sudo systemctl restart vsftpd
### Paso 4: Habilitar la carga de archivos
1. Descomentamos la línea write_enable=YES para permitir la carga de archivos:

   ```bash
    #write_enable=YES
2. Reiniciamos el servidor nuevamente.

### Paso 5: Configurar Chroot para los usuarios
1. Habilitamos la cárcel (chroot) para usuarios locales:

   ```bash
    chroot_local_user=YES
2. Para restringir el acceso solo a su directorio, creamos un subdirectorio:

- mkdir /home/userftp/ftp
- mkdir /home/userftp/ftp/upload
- chmod a-w /home/userftp/ftp
- chmod u+w /home/userftp/ftp/upload
3. Configuramos el archivo vsftpd.conf:

   ```bash
    user_sub_token=userftp
    local_root=/home/userftp/ftp
### Paso 6: Restricción de usuarios
1. Configuramos el archivo /etc/vsftpd.userlist para permitir acceso solo a userftp:

   ```bash
    echo "userftp" | sudo tee -a /etc/vsftpd.userlist
2. Reiniciamos el servicio para aplicar los cambios:

   ```bash
    sudo systemctl restart vsftpd
### Paso 7: Comprobación del acceso FTP
1. Intentamos conectarnos como usuario anónimo (debería fallar).

2. Probamos conexión como userftp y verificamos acceso y carga de archivos.

- Comandos de prueba:
   ```bash
    get pruebaftp.txt (descargar archivo)
    put archivo.txt (subir archivo)
3. Verificamos que los archivos se descarguen correctamente en el directorio local y con los permisos adecuados.

### Resumen de la configuración final
- Solo usuarios locales pueden conectarse.
- Carga de archivos habilitada con umask 022.
- Directorios de acceso restringidos usando chroot.
- Sólo userftp puede conectarse.


# Práctica 3 - Configuración de servidor FTP con Cifrado

En esta práctica, aprenderemos cómo asegurar la conexión usando el protocolo SSL/TLS, lo que permitirá transferir datos encriptados a través de FTP.

Recuerda que existen dos modos de conexión cifrada de FTP: **FTPS** y **SFTP**, los cuales no están relacionados entre sí en cuanto a configuración y funcionamiento.

## FTPS (File Transfer Protocol Secure)

Es a FTP lo que HTTPS es a HTTP. El servidor FTP define sus claves pública y privada, las cuales comparte con el cliente para establecer un canal seguro. Para habilitar esta conexión cifrada, es necesario generar un certificado SSL y configurar el servidor `vsftpd`.

Existen dos tipos de conexiones FTPS:

- **FTPS Explícito (FTPES):** El cliente solicita la conexión segura mediante un comando (por ejemplo, `AUTH TLS`).
- **FTPS Implícito (FTPIS):** La conexión segura se establece automáticamente al conectarse al puerto 990.

## SFTP (SSH File Transfer Protocol)

En este caso, la conexión se realiza a través de un canal SSH, y `vsftpd` no necesita configuración especial. La conexión usa el puerto 22 para SSH, y los datos se transfieren a través de ese canal cifrado.

---

## Instancia en AWS

### Iniciar la instancia AWS P4-vsftpd

Asegúrate de que la instancia creada previamente esté en funcionamiento, ya que contiene el servidor `vsftpd` instalado y los usuarios con permisos de FTP.

### Comprobar estado del servicio vsftpd:

```bash
sudo systemctl status vsftpd
```

Si no está en ejecución, inicia y habilita el servicio:

```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd.service
```

## Configuración de FTPS (FTP sobre SSL/TLS)

### Generar un certificado SSL autofirmado

Usaremos OpenSSL para generar un certificado SSL y su clave privada. Ejecuta el siguiente comando:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

Esto generará un certificado autofirmado válido por 365 días.

### Configurar el servidor vsftpd para habilitar SSL/TLS

Realiza una copia de seguridad del archivo de configuración:

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup2
```

Edita el archivo de configuración:

```bash
sudo nano /etc/vsftpd.conf
```

Modifica las siguientes configuraciones:

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

### Habilitar el modo pasivo

Para evitar problemas con conexiones pasivas, define puertos específicos en el archivo `vsftpd.conf`:

```bash
pasv_enable=YES
pasv_min_port=1027
pasv_max_port=1030
pasv_address=X.X.X.X
```

`pasv_address` debe ser la IP pública de tu servidor. Si no se configura, se usará la IP local.

### Reiniciar el servicio vsftpd:

```bash
sudo systemctl restart --now vsftpd
```

## Comprobación de la conexión FTP al servidor vsftpd

### Acceso con Cliente FTP en Modo Comando:

Abre una terminal y escribe el siguiente comando para conectarte al servidor FTP:

```bash
ftp nombre_de_host_ftp
```

Reemplaza `nombre_de_host_ftp` con la dirección IP pública de tu servidor FTP.

Ingresa el usuario y la contraseña (por ejemplo, `userftp` y `ieselcaminas`). Si la conexión es exitosa, deberías obtener el prompt `ftp>`.

### Acceso con Cliente FTP Gráfico (FileZilla):

Abre FileZilla y crea una nueva conexión usando los siguientes parámetros:

- **Protocolo:** FTP
- **Cifrado:** Usar FTPS Explícito
- **Puerto:** 21 (por defecto)
- **Servidor:** Dirección IP pública de tu servidor.
- **Usuario:** userftp
- **Contraseña:** ieselcaminas

Acepta el certificado SSL generado al intentar conectar.

## SFTP (SSH File Transfer Protocol)

Para utilizar SFTP, no necesitas configurar `vsftpd`. La conexión se realiza a través de SSH en el puerto 22. Aquí se explican los pasos:

- Generar un par de claves SSH para el usuario `user2`, si aún no lo has hecho.

### Verificar la conexión SSH:

Asegúrate de que la conexión SSH funcione correctamente antes de intentar la conexión SFTP:

```bash
ssh -i clave user2@ipserver
```

### Conexión SFTP en modo comando:

Para conectarte por SFTP en modo comando:

```bash
sftp user2@ipserver
```

### Conexión SFTP en FileZilla:

Si prefieres usar FileZilla, configura la conexión como SFTP con el puerto 22. Asegúrate de indicar la ubicación de la clave privada para que la conexión funcione correctamente.

---

## Resumen de la configuración final:

- **FTPS:** La conexión segura se establece utilizando el protocolo SSL/TLS en `vsftpd`. Los datos se cifran durante la transferencia.
- **SFTP:** Se utiliza el canal SSH para realizar las transferencias, y `vsftpd` no requiere configuración adicional.


# Práctica 4: Dockerizar un servidor FTP

En esta práctica, se configurará un servidor FTP utilizando Docker, basándonos en la imagen `fauria/vsftpd`. El objetivo es crear un contenedor con las mismas características que un servidor FTP básico.

## Iniciar contenedor FTP básico

Comenzamos con un contenedor que permita el acceso FTP simple:

```bash
docker run     --rm     -p 21:21 -p 20:20     -d     --name pruebavsftpd     fauria/vsftpd
```

Recupera el usuario y contraseña con:

```bash
docker logs pruebavsftpd
```

Luego, conecta con FileZilla utilizando el modo **Activo**.

## Montar directorios en el host

Para evitar que los archivos se borren al detener el contenedor, monta el directorio de datos en el host:

```bash
mkdir datosftp
docker run     --rm     -p 21:21 -p 20:20     -d     --name pruebavsftpd     -v /home/admin/datosftp:/home/vsftpd     fauria/vsftpd
```

Conéctate nuevamente con FileZilla y verifica que los archivos se guardan en `/home/admin/datosftp/`.

## Configuración con usuario FTP y modo pasivo

A continuación, creamos un usuario `userftp` y activamos el modo pasivo:

```bash
docker run     --rm     -e FTP_USER=userftp     -e FTP_PASS=ieselcaminas     -e PASV_MIN_PORT=21100     -e PASV_MAX_PORT=21110     -p 21:21 -p 21100-21110:21100-21110     -d     --name pruebavsftpd     -v /home/admin/datosftp:/home/vsftpd     fauria/vsftpd
```

Conéctate con FileZilla usando el modo **Pasivo**.

## Configuración de permisos de archivos

Para ajustar los permisos de los archivos subidos, establecemos la variable `LOCAL_UMASK`:

```bash
docker run     -e FTP_USER=userftp     -e FTP_PASS=ieselcaminas     -e PASV_MIN_PORT=21100     -e PASV_MAX_PORT=21110     -e LOCAL_UMASK=022     -p 21:21 -p 21100-21110:21100-21110     -d     --name vsftpdsincifrado     -v /home/admin/datosftp:/home/vsftpd     fauria/vsftpd
```

Verifica que los archivos subidos tengan los permisos correctos.

## Dockerizar vsftpd con CIFRADO (FTPS)

Para habilitar FTPS, creamos una imagen personalizada copiando los archivos de configuración y certificado:

1. Genera un certificado SSL:
   ```bash
   openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout vsftpd.pem -out vsftpd.pem
   ```

2. Copia el archivo `vsftpd.conf` desde un contenedor ya configurado sin cifrado:

   ```bash
   docker cp vsftpdsincifrado:/etc/vsftpd/vsftpd.conf .
   ```

   Modifica `vsftpd.conf` para habilitar FTPS.

3. Crea un `Dockerfile` para generar una imagen personalizada:

   ```dockerfile
   FROM fauria/vsftpd
   COPY vsftpd.conf /etc/vsftpd/vsftpd.conf
   COPY --chown=root:root vsftpd.pem /etc/vsftpd/vsftpd.pem
   ```

4. Construye y corre la imagen:

   ```bash
   docker build -t mivsftpd .
   docker run        -e FTP_USER=userftp        -e FTP_PASS=ieselcaminas        -e PASV_MIN_PORT=21100        -e PASV_MAX_PORT=21110        -e LOCAL_UMASK=022        -p 21:21 -p 21100-21110:21100-21110        -d        --name vsftpdconcifrado        -v /home/admin/datosftp:/home/vsftpd        mivsftpd
   ```

Conéctate usando **FTP explícito sobre TLS** y **modo pasivo**.

## Conclusión

Hemos dockerizado un servidor FTP con configuraciones de seguridad básicas y avanzadas (modo pasivo y FTPS).
