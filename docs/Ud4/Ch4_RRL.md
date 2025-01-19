---
title: 'CH4 - RRL'
---

# Cheatsheet DNS y LDAP

## DNS

El **DNS** traduce nombres de dominio legibles por humanos a direcciones IP numéricas.

### DNSUTILS

```bash
sudo apt install dnsutils
```

Gracias a este paquete podemos utilizar herramientas para la administración y resolución de DNS en sistemas Linux.

```bash
nslookup $direccion

dig  $direccion

host $direccion
```

### BIND9

```bash
sudo apt-get install bind9 bind9utils bind9-doc 
```

Gracias a este comando podemos usar el servidor DNS **bind9** y varias utilidades que nos ayudarán a administrarlo y configurarlo.

#### ARCHIVOS DE CONFIGURACIÓN BIND9

* `/etc/bind/named.conf` ->  Este archivo contiene las directrices básicas y referencias a otros archivos de configuración utilizados por el servidor.

* `/etc/bind/named.conf.options` -> Aquí es donde se configuran opciones globales y parámetros principales para el servidor DNS.

* `/etc/bind/named.conf.local` -> En este archivo se definen las zonas DNS personalizadas para el servidor DNS.

* `/etc/bind/named.conf.default-zones` -> Aquí se definen las zonas DNS predeterminadas.

* `/etc/bind/db.example.com` -> Archivo de zona en el que se encuentran los registros de recursos.

##### CONFIGURACIÓN DEL ARCHIVO DE ZONA

```bash
$TTL 1d
$ORIGIN example.com. ; dominio base
@   IN  SOA     ns1.example.com. admin.example.com. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
; Servidores de nombres (Name Servers)
    IN  NS      ns1.example.com.
; Registros A (direcciones IP)
ns1 IN  A       3.85.104.173
www IN  A       3.85.104.173
```

##### CONFIGURACIÓN DEL ARCHIVO DE /etc/bind/named.conf

```bash
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";
allow-query { any; };  // Permitir consultas desde cualquier IP
```

##### CONFIGURACIÓN DEL ARCHIVO DE /etc/bind/named.conf.options

```bash
options {
    recursion no; // Desactiva la recursividad
    forwarders {
        8.8.8.8;  // Google DNS 1
        8.8.4.4;  // Google DNS 2
    };
};
```

#### COMANDOS DE ADMINISTRACIÓN BIND9

* `sudo named-checkconf` -> Verifica la sintaxis del archivo de configuración.

* `sudo named-checkzone example.com /etc/bind/db.example.com` -> Verifica la sintaxis y la validez del archivo de la zona.

## LDAP

Los directorios **LDAP** se utilizan comúnmente para almacenar información sobre usuarios, grupos, dispositivos y otros objetos, organizados de manera jerárquica.

```bash
sudo apt install slapd ldap-utils
```

Este comando instala los paquetes necesarios para configurar un servidor **LDAP**.

```bash
sudo dpkg-reconfigure slapd
```

Este comando se usa para reconfigurar el servidor **LDAP**, permite establecer parámetros como el dominio de base, el administrador y las configuraciones de seguridad.

### ARCHIVOS DE CONFIGURACIÓN LDAP

* `/etc/ldap/ldap.conf` -> Configuración principal del cliente LDAP.

* `/etc/slapd/slapd.conf` -> Configuración del servidor SLAPD, que es el servidor LDAP que se utiliza para gestionar directorios LDAP.

* `ejemplo.ldif` ->  Los archivos LDIF son archivos de texto que contienen registros que representan entradas de un directorio LDAP.

### COMANDOS PARA ADMINISTRAR LDAP

* `sudo slapcat` -> Exporta el contenido de un servidor LDAP a un archivo en formato LDIF.

* `ldapadd -x -D "cn=admin,dc=ejemplo,dc=local" -w contraseña -f ejemplo.ldif` -> Añadir objetos a la base LDAP desde un archivo LDIF.

* `ldapsearch -x -b "dc=ejemplo,dc=local" "(uid=*usuario*)"` -> Buscar entradas LDAP que coincidan con la expresión `(uid=*usuario*)`.

* `ldapmodify -x -D cn=admin,dc=ejemplo,dc=local -w contraseña -f cambiarUsuario.ldif` ->  Se utiliza para realizar modificaciones en el directorio LDAP

* `ldapdelete -x -D cn=admin,dc=ejemplo,dc=local -w contraseña uid=alu02,ou=usuarios,dc=ejemplo,dc=local` -> Elimina la entrada especificada del directorio LDAP

#### ARCHIVO LDIF

```bash
dn: dc=empresa,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: Proyecto Empresa
dc: empresa

dn: ou=people,dc=empresa,dc=local
objectClass: organizationalUnit
ou: people

dn: ou=groups,dc=empresa,dc=local
objectClass: organizationalUnit
ou: groups
```

#### LDAPADD

```bash
ldapadd -x -D "cn=admin,dc=ejemplo,dc=local" -w contraseña -f ejemplo.ldif
```

* `-x` -> Autenticación simple.

* `-D` -> Define el usuario administrador LDAP **(DN)**.

* `-w` -> Contraseña del usuario LDAP.

* `-f` -> Especifica el archivo **LDIF** a usar.

#### LDAPSEARCH

```bash
ldapsearch -x -b "dc=ejemplo,dc=local" "(uid=*usuario*)"
```

* `-x` -> Autenticación simple.

* `-b` -> Define la base de búsqueda.

* `(uid=*usuario*)` -> Expresión de búsqueda.

#### LDAPMODIFY

```bash
ldapmodify -x -D cn=admin,dc=ejemplo,dc=local -w contraseña -f cambiarUsuario.ldif
```

* `-x` -> Autenticación simple.

* `-D` -> Define el usuario administrador LDAP **(DN)**.

* `-w` -> Contraseña del usuario LDAP.

* `-f` -> Especifica el archivo **LDIF** a usar.

#### LDAPDELETE

```bash
ldapdelete -x -D cn=admin,dc=ejemplo,dc=local -w contraseña uid=alu02,ou=usuarios,dc=ejemplo,dc=local
```

* `-x` -> Autenticación simple.

* `-D` -> Define el usuario administrador LDAP **(DN)**.

* `-w` -> Contraseña del usuario LDAP.

### AUTENTIFICACIÓN WEB CON LDAP

Podemos configurar un servidor web para que utilice un servidor LDAP como fuente de autenticación, lo que significa que las credenciales de los usuarios serán validadas contra la base de datos LDAP.

#### APACHE2

Primero que todo, tenemos que activar un módulo.

```bash
sudo a2enmod authnz_ldap
```

Ahora vamos a implementar la autentificación en el archivo de configuración.

```bash
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory "/var/www/html/test">
        AuthType Basic
        AuthName "Apache LDAP authentication"
        AuthBasicProvider ldap
        AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=ejemplo,dc=local?uid?sub"
        AuthLDAPBindDN "cn=admin,dc=ejemplo,dc=local"
        AuthLDAPBindPassword "contraseña"
        Require valid-user
    </Directory>
</VirtualHost>
```

* `AuthType Basic` -> Define la autentificación.

* `AuthBasicProvider ldap` -> Define que se usara LDAP para la autentificación.

* `AuthLDAPURL` -> Define la URL del servidor LDAP.

* `AuthLDAPBindDN` -> Especifica el usuario.

* `Require valid-user` -> Obliga a que el usuario introducido sea válido.

#### NGINX

Primero tenemos que instalar el módulo de autentificación LDAP.

```bash
sudo dpkg -i libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb

ls -la /etc/nginx/modules-enabled/
lrwxrwxrwx 1 root root   58 Nov 27 19:01 50-mod-http-auth-ldap.conf -> /usr/share/nginx/modules-available/mod-http-auth-ldap.conf
```

Ahora vamos a modificar el archivo de configuración.

```bash
http {
    ldap_server mi_servidor_ldap {
        url ldap://IPSERVIDOR/ou=usuarios,dc=ejemplo,dc=local?uid?sub;
        binddn "cn=admin,dc=ejemplo,dc=local";
        binddn_passwd "contraseña";
        group_attribute member;
        group_attribute_is_dn on;

        require valid_user;
    }

    include /etc/nginx/sites-enabled/*;
}
```

Ahora vamos a modificar el archivo de nuestro sitio virtual en `sites-available`.

```bash
server {
    listen 80;
    server_name sitioldap;
    root /var/www/sitioldap;
    index index.html;

    # Configuración LDAP
    location / {
        try_files $uri $uri/ =404;
        auth_ldap "Protected Area";
        auth_ldap_servers mi_servidor_ldap;
    }
}
```

## PHPLDAPADMIN

```bash
sudo apt install phpldapadmin
```

Este comando instala **phpldapadmin**, una herramienta web para administrar un servidor LDAP de manera gráfica.
