---
title: 'Ch4 - ACF'
---

# 🌐 Servicios de red DNS y LDAP

### 1. ⚙️ Comandos de instalación 

| Comando | Explicación |
|----------|----------|
| `apt-get install dnsutils` | 🔍 Comando para instalar herramientas dig, host y nslookup |
| `apt-get install bind9 bind9utils bind9-doc ` | 📡 Comando para instalar un servidor DNS en Debian |
| `apt-get install slapd ldap-utils` | 🛡️ Comando para instalar un servidor OpenLDAP |
|`sudo apt-get install phpldapadmin`| 🛠️ Comando para instalar Phpldapadmin|
|`sudo systemctl restart nginx`| ♻️ Reiniciar el servicio de nginx|


### 2. 🧰 Comandos de configuración y comprobación

| Comando | Explicación |
|----------|----------|
| `sudo named-checkzone deaw.es /etc/bind/db.deaw.es` | 🔧 Comando para comprobar que no hay errores en el archivo de creación de zona |
|`nslookup 3.85.104.173`| 🔎 Comando para consultar el nombre de dominio|
|`dig -x 3.85.104.173`|🗺️ Comando para hacer una consulta inversa para obtener el nombre de dominio|
|`sudo slapcat`|📋 Comando para ver la información actual de la base de datos|
|`sudo dpkg-reconfigure slapd`|⚙️ Comando para configurar nuestro directorio|

📝 Ejemplo de archivo de configuración del archivo named.conf.local:
```
zone "deaw.es" {
        type master;
        file "/etc/bind/db.deaw.es";  //Ruta donde ubicamos nuestro archivo de zona
};
```
📝 Ejemplo de archivo de creación de zona:
```
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
📝 Ejemplo de archivo de configuración de php:
```
$servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
$servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
```
### 3. 🛠️ Comandos de LDAP

| Comando | Explicación |
|----------|----------|
| `ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f estructura_basica.ldif` | 📂 Comando para añadir unidades organizativas, usuarios o grupos a partir de un archivo ldif |
|`ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif`| ✏️ Comando para modificar un parámetro de unidades organizativas, usuarios o grupos a partir de un archivo ldif|
|`ldapsearch -x -b dc=proyecto-empresa,dc=local "(uid=*profe*)"`|🔍 Comando para buscar unidades organizativas, usuarios o grupos|
|`ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local`|❌ Comando para borrar unidades organizativas, usuarios o grupos|

📝 Ejemplo de archivo de creación de unidades organizativas:
```
# Usuarios
dn: ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: usuarios
```

📝 Ejemplo de archivo de creación de grupos:
```
#Grupo profesores
dn: cn=profesores,ou=grupos,dc=proyecto-empresa,dc=local
objectClass: posixGroup
cn: profesores
gidNumber: 10000
```
📝 Ejemplo de archivo de creación de usuarios:
```
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
```
### 4. 🔒 Comandos de Autenticación Apache contra LDAP

| Comando | Explicación |
|----------|----------|
| `sudo a2enmod authnz_ldap` | 🛡️ Comando para habilitar el módulo de autenticación |

📝 Ejemplo de archivo .htacces:
```
    AuthType Basic
    AuthName "Apache LDAP authentication"
    AuthBasicProvider ldap
    AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub"
    Require valid-user
```
📝 Ejemplo de archivo ldap.conf:
```
    BINDDN cn=admin,dc=daw,dc=ieselcaminas
    BINDPW ieselcaminas
```
