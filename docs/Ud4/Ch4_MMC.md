---
title:'CH4 - MMC'
---

# CheatSheet DNS y LDAP Marcel Mañas

## Herramientas de Diagnóstico de Servidor de Nombres 🔍

| sudo apt-get install dnsutils | Instalar: dig, host y nslookup |
| --- | --- |
| host (zona) // dig (zona) // nslookup (zona) | Consulta de nombre. |

## Servidor DNS ☁️

| sudo apt-get install bind9 bind9utils bind9-doc | Instalar DNS |
| --- | --- |
| /etc/bind/named.conf | Archivo de configuración de Bind |

### Configuración *named.conf.options ⛈️*

| allow-query { any; }; | Añadir  a *named.conf.options* para aceptar todas las solicitudes |
| --- | --- |
| sudo named-checkconf | Comprobar si las modificaciones son correctas |
| recursion no; | Añadir a *named.conf.options* para eliminar la recursividad. |
| forwarders {   
 8.8.8.8;   
 8.8.4.4;
}; | Añadir a *named.conf.options* para realizar consultas recursivas a un DNS propio |

### Configuración *named.conf.local ⛅*

| zone "deaw.es" {
        type master;
        file "/etc/bind/db.deaw.es";  //Ruta donde ubicamos nuestro archivo de zona
}; | Añadir  a *named.conf.local* para declarar zonas. |
| --- | --- |

### Creación zonas 🌤️

| $TTL 1d
$ORIGIN [deaw.es](http://deaw.es/). ; base domain-name
@   IN  SOA     [ns1.deaw.es](http://ns1.deaw.es/). [admin.deaw.es](http://admin.deaw.es/). (
 2023112301  ; Serial
  8H           ; Refresh
  2H           ; Retry
  4W          ; Expire
  1D )         ; Minimum TTL
; Name Servers
  IN  NS      [ns1.deaw.es](http://ns1.deaw.es/).
; A records
  ns1 IN  A       3.85.104.173
  www IN  A       3.85.104.173 | Zona resolución directa |
| --- | --- |
| $TTL 1d
$ORIGIN 104.85.3.IN-ADDR.ARPA.
@   IN  SOA     [ns1.deaw.es](http://ns1.deaw.es/). [admin.deaw.es](http://admin.deaw.es/). (
  2023112301  ; Serial
  8H           ; Refresh
  2H           ; Retry
  4W          ; Expire
  1D )         ; Minimum TTL

; Name Servers
  IN  NS      [ns1.deaw.es](http://ns1.deaw.es/).

; PTR record
  173 IN  PTR     [ns1.deaw.es](http://ns1.deaw.es/). ; fully qualified domain name (FQDN) | Zona de resolución inversa |

## Servidor LDAP 🕸️

| sudo apt-get install slapd ldap-utils | Instalar OpenLDAP |
| --- | --- |
| sudo slapcat | Información de la base de datos |
| sudo dpkg-reconfigure slapd | Configurar OpenLDAP |
| sudo apt-get install phpldapadmin | Instalar app web para modificar la base de datos. Acceso: [http://IPSERVER/phpldapadmin](http://ipserver/phpldapadmin) |
| sudo nano /etc/phpldapadmin/config.php | Fichero de configuración de phpLDAPadmin |

### Creación de base de datos mediante ficheros .ldif 🏙️

| # Usuarios
   dn: ou=usuarios,dc=proyecto-empresa,dc=local
   objectClass: organizationalUnit
   ou: usuarios

# Grupos
   dn: ou=grupos,dc=proyecto-empresa,dc=local
   objectClass: organizationalUnit
   ou: grupos | Fichero .ldif con 2 unidades organizativas (ou). |
| --- | --- |
| ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f estructura_basica.ldif | Incorporación de los datos .ldif a la base de datos |
| ldapsearch -x -b dc=proyecto-empresa,dc=local "(búsqueda)" | Buscar en la base de datos |
| $ ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local | Borrar registros de la base de datos |

## Autentificación Apache con LDAP 🔐

| sudo a2enmod authnz_ldap | Habilita el módulo de autenticación LDAP Apache2 |
| --- | --- |
| <Directory "/var/www/html/test"> 
        AuthType Basic
        AuthName "Apache LDAP authentication"
        AuthBasicProvider ldap 
        AuthLDAPURL ldap://host:port/basedn?attribute?scope?filter [NONE|SSL|TLS|STARTTLS]
        AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
        AuthLDAPBindPassword ieselcaminas
        Require valid-user
        </Directory> | Líneas de 000-default.conf para habilitar LDAP. |

## Autentificación Nginx con LDAP 🔐

| sudo dpkg -i libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb | Módulo de autentificación |
| --- | --- |
| http {
  ldap_server mi_servidor_ldap {
    url  ldap://IPSERVIDOR/ou=usuarios,dc=daw,
           dc=ieselcaminas?uid?sub;
    binddn "cn=admin,dc=daw,dc=ieselcaminas";
    binddn_passwd "ieselcaminas";
    group_attribute member;
    group_attribute_is_dn on;

     require valid_user;
 }

 include /etc/nginx/sites-enabled/*;
} | Líneas de /etc/nginx/nginx.conf para habilitar LDAP. |
| server {
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
} | Modificación de /etc/nginx/sites-available/sitioldap para la utilización de LDAP. |
| sudo nginx -t | Comprobación de la  configuración |
