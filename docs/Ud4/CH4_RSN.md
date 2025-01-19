---
title: 'CH4 - RSN'
---

## Configuración servidor DNS ▶️

| Comandos 🛡️ | Explicación 🗨️ |
-------------------- | -----------------------
| `sudo apt-get install dnsutils` | Instala als herramientas necesarias para el DNS |
| `sudo apt-get install bind9 bind9utils bind9-doc` | Instalación de servidor DNS |
| `sudo named-checkzone deaw.es /etc/bind/db.deaw.es` | Comprobar el estado de la zona |

## Dockerización DNS 📦

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `docker run -d --named=miserverdns --publish 53:53/udp --publish 53:53/tcp internetsystemsconsortium/bind9:9.18` | Creación del contenedor dns|
| `docker cp "nombre archivo" "nombre contenedor":/etc/bind` | Copiar archivos locales al contenedor |

## Configuración de un servidor LDAP 🖥️

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo apt-get install slapd ldap-utils` | Instalar OpenLDAP y sus utilidades |
| `sudo slapcat` | Muestra la información de la base de datos |
| `sudo dpkg-reconfigure slapd` | Reconfiguración del servidor LDAP  | 

## Carga de datos en servidor LDAP mediante ficheros ldif 🔁

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas  -f "archivo.ldif"` | Añadir información a la base de datos|
| `ldapmodify "datos anteriores"` | Modificación ldif |
| `ldapsearch -x -b` | Búsqueda en la base de datos |
| `ldapdelete -x -D` | Borrar registros de la base de datos |

## Dockerización LDAP 📦

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `docker run --env LDAP_ORGANISATION="My Company" --env LDAP_DOMAIN="my-company.com" --env LDAP_ADMIN_PASSWORD="JonSn0w" --detach osixia/openldap:1.5.0` | Lanzar contenedor ldap |
| `docker network create ldap-network` | Crear network para el ldap |

## Autenticación Apache contra LDAP 🪶✅

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo a2enmod authnz_ldap` | Habilitar módula de autenticación |
| `sudo mkdir /var/www/html/test` | Crear directorio test |
| `sudo nano /var/www/html/test/index.html` | Crear html |
| `sudo chown -R www-data:www-data /var/www/html/test ` | Cambia el usuario y grupo del directorio |

## Autenticación apache dockerizada 🪶📦

| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- 
| `docker run -d --name apache2_server --net ldap-network -d -p 80:80 -e LDAP_BIND_ON= cn=admin,dc=daw,dc=ieselcaminas  -e LDAP_PASSWORD=ieselcaminas -e LDAP_URL=ldap://ldap-service ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub my-apache2` | Creación del contenedor ldap |


## Autenticación Nginx contra LDAP ✅
| Comandos 🛡️ | Explicación 🗨️ |
| -------- | ----------- |
| `sudo mkdir -p /var/www/sitioldap` | Creación carpeta ldap |
| `sudo nano /var/www/sitioldap/index.html` | Creación index.html en ldap |
| `sudo nano /etc/nginx/sites-available/sitioldap` | Creación sitio virtual |
| `sudo ln -s /etc/nginx/sites-available/sitioldap /etc/nginx/sites-enabled/` | Activación sitio virtual |
| `` |  |

## Configuraciones de archivos 📜


```c
> Archivo configuración named.conf (P1)

// This is the primary configuration file for the BIND DNS server named.
//
// Please read /usr/share/doc/bind9/README.Debian for information on the
// structure of BIND configuration files in Debian, *BEFORE* you customize
// this configuration file.
//
// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";
```

```c
> Archivo configuración named.conf.local (P1)

//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "deaw.es" {
        type master;
        file "/etc/bind/db.deaw.es";  //Ruta donde ubicamos nuestro archivo de zona
};
```


```c
> Archivo configuración de db.deaw.es (P1)

$TTL 1d
$ORIGIN deaw.es. ; base domain-name
@   IN  SOA     ns1.deaw.es. admin.deaw.es. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
; Name Servers
    IN  NS      ns1.deaw.es.
; A records
ns1 IN  A       3.85.104.173
www IN  A       3.85.104.173
```

```c
> Archivo configuración de zona inversa (P1)

zone "104.85.3.in-addr.arpa" {
    type master;
    file "/etc/bind/db.3.85.104";  # Ruta al archivo de zona inversa
};
```

```c
> Archivo configuración de db.3.85.104 (P1)

$TTL 1d
$ORIGIN 104.85.3.IN-ADDR.ARPA.
@   IN  SOA     ns1.deaw.es. admin.deaw.es. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL

; Name Servers
    IN  NS      ns1.deaw.es.

; PTR record
173 IN  PTR     ns1.deaw.es. ; fully qualified domain name (FQDN)
```

```ldif
> Archivo configuración estructura_basica.lidf (P1)

# Usuarios
dn: ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: usuarios

# Grupos
dn: ou=grupos,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: grupos
```

```ldif
> Archivo configuración grupos.lidf (P1)

#Grupo profesores
dn: cn=profesores,ou=grupos,dc=proyecto-empresa,dc=local
objectClass: posixGroup
cn: profesores
gidNumber: 10000

#Grupo alumnos
dn: cn=alumnos,ou=grupos,dc=proyecto-empresa,dc=local
objectClass: posixGroup
cn: alumnos   
gidNumber: 10001
```
```ldif
> Archivo de configuración de los usuarios
# Profe01
dn: uid=profe01,ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
uid: profe01
cn: profe01
sn: DAW
loginShell: /bin/bash
uidNumber: 1001
gidNumber: 10000
homeDirectory: /home/profe01
gecos: Profe01 DAW
userPassword: 123456

# Profe02
dn: uid=profe02,ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: inetOrgPerson
objectClass: posixAccount
uid: profe02
cn: profe02
sn: DAW
loginShell: /bin/bash
uidNumber: 1002
gidNumber: 10000
homeDirectory: /home/profe02
gecos: Profe02 DAW
userPassword: 123456
```

```html
> Html del index.html

<!DOCTYPE html>
<html>
<head>
<title>Sitio LDAP!</title>
</head>
<body>
<h1>Bienvendido al sitio validado por LDAP!</h1>
</body>
</html>
````

```c
> Archivo configuración 000-default.conf

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/html/test"> 
        AuthType Basic
        AuthName "Apache LDAP authentication"
        AuthBasicProvider ldap 
        AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub" 
        AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
        AuthLDAPBindPassword ieselcaminas
        Require valid-user
        </Directory>

</VirtualHost>
```


```dockerfile
> Configuración dockerfile

# Usamos la imagen base de PHP 7 con Apache
FROM php:7-apache

# Activamos el módulo LDAP de Apache ejecutando el siguiente comando
RUN a2enmod authnz_ldap

# Creamos el directorio test como en la práctica anterior
RUN mkdir -p /var/www/html/test

# Copiamos los ficheros como los de la práctica anterior
COPY index.html /var/www/html/test/index.html

# Copiamos el archivo de configuración del sitio por defecto
COPY 000-default.conf /etc/apache2/sites-enabled/000-default.conf
```
```html
> Configuración index.html de nginx

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

```c
> Configuración sitio ldap de nginx
server {
    listen 80;
    server_name sitioldap;
    root /var/www/sitioldap;
    index index.html;

    location / {
         try_files $uri $uri/ =404;
    }
}

http {
    ldap_server mi_servidor_ldap {
        url ldap://IPSERVIDOR/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub;
        binddn "cn=admin,dc=daw,dc=ieselcaminas";
        binddn_passwd "ieselcaminas";
        group_attribute member;
        group_attribute_is_dn on;

        require valid_user;
    }

    include /etc/nginx/sites-enabled/*;
}
```




> Todas las conexiónes se han realizado desde el servidor de AWS academy
