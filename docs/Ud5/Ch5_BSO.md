---
title: 'CH5 - BSO'
---

# Servicios de red FTP

## Comandos Básicos para FTP

### Establecer Conexión con el Servidor FTP

```bash
ftp                # Inicia el cliente FTP.
user [usuario] [contraseña]  # Inicia sesión con un usuario autenticado.
open [servidor]    # Conecta con un servidor FTP.
```

### Transferencia de Archivos

```bash
get archivo # Descarga un archivo desde el servidor al equipo local.
put archivo # Sube un archivo desde el equipo local al servidor.
```

### Modo Activo y Pasivo

```bash
PASV   # Cambia al modo pasivo.
PORT   # Cambia al modo activo.
```

## Configuración del Servidor vsFTPd

### Instalación del Servidor

```bash
sudo apt install vsftpd -y
```

### Iniciar y Habilitar el Servicio vsftpd

```bash
sudo systemctl start vsftpd #Inicializa el servicio.
sudo systemctl enable vsftpd #Habilita el servicio para arrancar automáticamente al iniciar el sistema.
```

### Configuración de vsFTPd

Editar el archivo de configuración:

```bash
sudo nano /etc/vsftpd.conf
```

configuraciones básicas:

```bash
anonymous_enable=NO # Deshabilita acceso anónimo.
local_enable=YES    # Habilita acceso a usuarios locales.
write_enable=YES    # Permite la escritura.

local_umask=022     # Define permisos por defecto para archivos.

chroot_local_user=YES # Restringe usuarios a sus directorios.
allow_writeable_chroot=YES # Permite escritura en chroot.

user_sub_token=$USER # Usa el nombre del usuario en el directorio.
local_root=/home/$USER/ftp

userlist_enable=YES  # Habilita lista de usuarios permitidos.
userlist_file=/etc/vsftpd.userlist
userlist_deny=NO     # Restringe el acceso a quienes no estén en la lista.
```

Se necesita reiniciar para aplicar cambios:

```bash
sudo systemctl restart vsftpd
```

### Configurar Usuarios

```bash
sudo adduser userftp # Crea un nuevo usuario.
sudo usermod --home /userdir/userftp userftp # Cambia el directorio home de un usuario.
echo "userftp" | sudo tee -a /etc/vsftpd.userlist # Añade el usuario a la lista de permitidos.
```

## Configuración de FTPS (FTP Seguro)

### Generar certificado SSL

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

### Habilitar cifrado SSL

Editar el archivo de configuración de vsFTPd:

```bash
sudo nano /etc/vsftpd.conf
```

```bash
# Certificados SSL
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES

# Seguridad
allow_anon_ssl=NO # Bloquea acceso anónimo con SSL.
force_local_data_ssl=YES # Obliga a usar cifrado para datos locales.
force_local_logins_ssl=YES # Obliga a usar cifrado para inicios de sesión.
ssl_tlsv1=YES # Habilita TLSv1.
ssl_sslv2=NO # Deshabilita SSLv2.
ssl_sslv3=NO # Deshabilita SSLv3.
require_ssl_reuse=NO # No requiere reutilizar sesiones SSL.
ssl_ciphers=HIGH # Usa cifrados fuertes.
```

### Configurar Modo Pasivo

```bash
pasv_enable=YES # Activa modo pasivo.
pasv_min_port=1027 # Puerto mínimo.
pasv_max_port=1030 # Puerto máximo.
pasv_address=ip_publica # Dirección IP pública o accesible desde el cliente.
```

Reiniciar el servicio:

```bash
sudo systemctl restart vsftpd
```

## Configuración de SFTP

### Copias de seguridad

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.ftps # Crea una copia del archivo de configuración
```

### Establecer conexión por SFTP

```bash
ssh -i clave user2@ipserver
```

### Restaurar archivo de configuración anterior

```bash
sudo cp /etc/vsftpd.conf.ftps /etc/vsftpd.conf
sudo systemctl restart vsftpd
```