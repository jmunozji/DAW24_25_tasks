---
title: 'CH4 - EV'
---

## **Configuración de un servidor DNS**

- Instalar herramientas de línea de comandos `dig`, `host` y `nslookup`
: `sudo apt-get install dnsutils`
- Comprobar el servidor de nombres de nuestra EC2
: `cat /etc/resolv.conf`
- Probar los comandos `dig`, `host` y `nslookup` junto con `cisco.com`
para ver la IP y el servidor DNS que nos la proporciona

### Instalación de servidor DNS

- Instalar el servidor DNS
: `sudo apt-get install bind9 bind9utils bind9-doc`
- Editar `/etc/systemd/resolved.conf`, añadir en [Resolve]
: `DNS= <IP_del_servidor_DNS>`
- Reiniciar systemd-resolved para aplicar la nueva configuración
: `sudo systemctl restart systemd-resolved`

### Configuración del servidor

- En `/etc/bind/named.conf.options` agregar
: `allow-query { any; };`
- Comprobar que el archivo de configuración es correcto
: `sudo named-checkconf`

#### Desactivar la recursividad

- Desactivar la recursividad incluyendo la siguiente directiva en
`/etc/bind/named.conf.options` : `recursion no;`

#### Configuración de los forwarder

- Agregar los números de IP de los servidores DNS deseados en la 
sección forwarders de `/etc/bind/named.conf.options`
```bash
    forwarders {
            8.8.8.8;
            8.8.4.4;
    };
```

### Configurar zonas directas

- En `/etc/bind/named.conf.local`, declarar la zona:
```bash
zone "example.com" {
    type master;
    file "/etc/bind/db.example.com";  # Ruta al archivo de zona inversa
};
```
- Configuración de la resolución inversa en el fichero descrito `/etc/bind/db.example.com`:
```bash
$TTL 1d
@   IN  SOA     ns1.example.com. admin.example.com. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL

; Name Servers
    IN  NS      ns1.example.com.
```

### Configurar zonas inversas

- En `/etc/bind/named.conf.local`, añadir:
```bash
zone "1.1.168.in-addr.arpa" {
    type master;
    file "/etc/bind/db.1.1.168";
};
```
- Contenido de `/etc/bind/db.1.1.168`:
```bash
$TTL 1d
@   IN  SOA     ns1.example.com. admin.example.com. (
              2023121501 ; Serial
              8H         ; Refresh
              2H         ; Retry
              4W         ; Expire
              1D )       ; Minimum TTL
    IN  NS      ns1.example.com.
1   IN  PTR     ns1.example.com.
```

## **Dockerización de un servidor DNS**

- Crear el contenedor:
```bash
docker run -d \
        --name=miserverdns \
        --publish 53:53/udp \
        --publish 53:53/tcp \
        internetsystemsconsortium/bind9:9.18
```
- Preguntar por un dominio y comprobar que resuleve correctamente
: `nslookup cisco.com 127.0.0.1`
- Para crear una zona igual que la de la práctica anterior, crear una carpeta `miserverdns` con estos ficheros:
```bash
named.conf
named.conf.options
named.conf.local
db.deaw.es
db.3.85.104 # Cambiar por el que corresponda a tu IP de resolución inversa
```
- Los ficheros tendrán el mismo contenido que anteriormente, excepto 
`named.conf`, que contendrá lo siguiente: 
```bash
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
```
- Copiar cada uno al interior del contenedor
: `docker cp named.conf miserverdns:/etc/bind ...`
- Parar y reiniciar el contenedor, o arrancarlo si estaba parado
y comprobar que resuelve correctamente

## **Configuración de un servidor LDAP**

- Instalar el paquete ldap-utils
: `sudo apt-get install slapd ldap-utils`
- Configurar nuestro directorio
: `sudo dpkg-reconfigure slapd`
- Ejecutar comando `slapcat` para verificar la base de datos
- Instalar phpldapadmin
: `sudo apt-get install phpldapadmin`
- Acceder desde el navegador web a "http://IPSERVER/phpldapadmin"
- Iniciar sesión con los datos de la salida de slapd
- Editar el fichero de configuración de phpldapadmin `/etc/phpldapadmin/config.php`
y sustituir las líneas
```bash
$servers->;setValue('server','base',array('dc=example,dc=com'));
$servers->setValue('login','bind_id','cn=admin,dc=example,dc=com');
```
por
```bash
$servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
$servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
```

### Crear unidades organizativas

- Desde PHP LDAP Admin, seleccionar `Create a child entry` > `Organizational Unit`
- Ejemplo en `estructura.ldif`:
```bash
dn: ou=usuarios,dc=example,dc=com
objectClass: organizationalUnit
ou: usuarios
```

### Crear grupos y usuarios

- Grupos (`grupos.ldif`):
```bash
dn: cn=profesores,ou=grupos,dc=example,dc=com
objectClass: posixGroup
cn: profesores
gidNumber: 5000
```
- Usuarios (`usuarios.ldif`):
```bash
dn: uid=usuario1,ou=usuarios,dc=example,dc=com
objectClass: posixAccount
uid: usuario1
cn: Usuario Uno
gidNumber: 5000
homeDirectory: /home/usuario1
userPassword: password
```

### Comandos útiles

- Añadir registros
: `ldapadd -x -D cn=admin,dc=example,dc=com -w <password> -f estructura.ldif`
- Modificar registros: 
```bash
dn: uid=usuario1,ou=usuarios,dc=example,dc=com
changetype: modify
replace: userPassword
userPassword: newpassword
```

## **Dockerización de servidor LDAP**

- Crear la red:
`docker network create ldap-network`
- Lanzar los contenedores:
```bash
docker run \
    --net ldap-network \
    --hostname ldap-service \
    --name ldap-service \
    --env LDAP_ORGANISATION="daw.ieselcaminas" \
    --env LDAP_DOMAIN="daw.ieselcaminas" \
    --env LDAP_ADMIN_PASSWORD="ieselcaminas" \
    --detach osixia/openldap

docker run \
    --net ldap-network \
    --name phpldapadmin-service \
    --publish 443:443 \
    --hostname phpldapadmin-service \
    --env PHPLDAPADMIN_LDAP_HOSTS=ldap-service \
    --detach osixia/phpldapadmin
```

## **Autenticación Apache contra LDAP**

- Habilitar el módulo de autenticación LDAP Apache2:
`sudo a2enmod authnz_ldap`
- Crear directorio `test`, crear dentro un archivo `index.html` y cambiar el usuario y el grupo del directorio `test` y su contenido a `www-data`:
```bash
sudo mkdir /var/www/html/test
sudo nano /var/www/html/test/index.html
sudo chown -R www-data:www-data /var/www/html/test
```
- Contenido del fichero `index.html`:
```bash
<!DOCTYPE html>
<html>
<head>
<title>Sitio LDAP!</title>
</head>
<body>
<h1>Bienvendido al sitio validado por LDAP!</h1>
</body>
</html>
```
- Modificar `/etc/apache2/sites-enabled/000-default.conf`:
```bash
        <Directory "/var/www/html/test"> 
        AuthType Basic
        AuthName "Apache LDAP authentication"
        AuthBasicProvider ldap 
        AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub" 
        AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
        AuthLDAPBindPassword ieselcaminas
        Require valid-user
        </Directory>
```

### Dockerización de Apache

- Crear Dockerfile con el contenido siguiente:
```bash
FROM php:7-apache

# Activamos el módulo LDAP de Apache ejecutando el siguiente comando
RUN a2enmod authnz_ldap

# Creamos el directorio test como en la práctica anterior
RUN mkdir -p /var/www/html/test

# Copiamos los ficheros como los de la práctica anterior
COPY index.html /var/www/html/test/index.html
COPY 000-default.conf /etc/apache2/sites-enabled/000-default.conf
```
- Crear imagen llamada `my-apache2`
- Lanzar el contenedor: 
```bash
docker run \
    --net ldap-network \
    --name apache2_server \
    -d \
    -p80:80 \
    my-apache2
```
