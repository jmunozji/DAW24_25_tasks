---
title: 'CH5 - MCG'
---

# cheatsheet 5 - ftp

este cheatsheet cubre comandos útiles para la instalación, configuración y manejo del servicio ftp utilizando `vsftpd` en sistemas basados en debian.

---

## instalación y actualización de paquetes

 **actualizar el índice de paquetes**  
   actualiza la lista de paquetes disponibles para la instalación.

```bash
sudo apt-get update
```
## actualizar los paquetes instalados

actualiza los paquetes a sus versiones más recientes.
```bash
sudo apt-get upgrade
```
## instalar vsftpd

instala el servidor ftp vsftpd, un servidor seguro y eficiente.
```bash
sudo apt-get install vsftpd
```
## gestión del servicio vsftpd

ver el estado del servicio vsftpd
muestra el estado actual del servicio vsftpd (si está activo o inactivo).
```bash
sudo systemctl status vsftpd
```
## reiniciar el servicio vsftpd y aplicar cambios

reinicia el servicio y lo pone en marcha inmediatamente.
```bash
sudo systemctl restart --now vsftpd
```
## configuración de ssl para vsftpd

generar un certificado ssl autofirmado
utiliza openssl para crear un certificado ssl que se usará en el servidor ftp. el comando genera un archivo vsftpd.pem que incluye tanto el certificado como la clave privada.
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout vsftpd.pem -out vsftpd.pem
```
## configurar vsftpd para usar ssl

modifica el archivo de configuración de vsftpd para habilitar ssl y especificar el archivo de certificado y la clave.
en el archivo de configuración /etc/vsftpd.conf, agrega o descomenta las siguientes líneas:

```
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.pem
ssl_enable=YES

```
configuraciones de seguridad ssl adicionales
asegura que solo se permita la conexión segura y que se utilicen configuraciones robustas para ssl/tls.

```
    allow_anon_ssl=NO                # no permitir ssl para conexiones anónimas
    force_local_data_ssl=YES         # forzar que los datos se transfieran solo a través de ssl
    force_local_logins_ssl=YES       # forzar que las conexiones de login se realicen solo con ssl
    ssl_tlsv1=YES                    # habilitar el uso de tls 1.0
    ssl_sslv2=NO                     # deshabilitar sslv2
    ssl_sslv3=NO                     # deshabilitar sslv3
    require_ssl_reuse=NO             # no requerir que se reutilice el mismo canal ssl
    ssl_ciphers=HIGH                 # usar solo los cifrados de alta seguridad
```

## configuración de modo pasivo en vsftpd
habilitar el modo pasivo
el modo pasivo es necesario para que el servidor ftp pueda funcionar correctamente detrás de un cortafuegos.
```bash
pasv_enable=YES
```
configurar el rango de puertos pasivos
define el rango de puertos que vsftpd utilizará para las conexiones pasivas. esto es importante cuando el servidor está detrás de un firewall.

```bash
pasv_min_port=1027    # puerto mínimo de conexión pasiva
pasv_max_port=1030    # puerto máximo de conexión pasiva
```
especificar la dirección ip del servidor en modo pasivo
define la ip pública o accesible desde el cliente para las conexiones pasivas. asegúrate de reemplazar X.X.X.X con la dirección ip correspondiente.
```bash
    pasv_address=X.X.X.X   # dirección ip pública o accesible
```
