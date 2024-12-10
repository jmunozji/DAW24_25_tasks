---
title: 'CH4 - MCG'
---
# cheatsheet 4 - dns's

## instalar cosas

### dnsutils

```bash
sudo apt-get install dnsutils
```

este comando instala el paquete `dnsutils`, que incluye herramientas útiles para la administración de DNS. las herramientas que nos interesan son:

```bash
nslookup  $direccion $direccionDNS
dig  $direccion
host $direcion
```

las 3 nos devulven información relevente respecto a dns de la dirección objetivo, con ligeras diferencias a la hora de presentarla. 

### bind9

```bash
sudo apt-get install bind9 bind9utils bind9-doc
```
este comando instala el servidor DNS BIND9 y varias utilidades necesarias para configurarlo y administrarlo.

### ldap
```bash
sudo apt-get install slapd ldap-utils
```
este comando instala los paquetes necesarios para configurar un servidor LDAP en linux

para poder configurar de forma guiada nuestro servicio ldap podemosusar el comando: 
```bash
sudo dpkg-reconfigure slapd
```

este comando se utiliza para reconfigurar el servidor LDAP (slapd) después de la instalación. permite establecer parámetros como el dominio base, el administrador, y las configuraciones de seguridad.

el mismo abrirá en nuestra consola una "interfaz gráfica" que nos irá guiando para que podamos configurar por primera vez o de nuevo nustro servidor ldap.

### phpldapadmin

```bash
sudo apt-get install phpldapadmin
```

este comando instala phpLDAPadmin, una herramienta web para administrar un servidor LDAP de manera gráfica. facilita la creación, modificación y eliminación de entradas en el directorio LDAP.

## administración de LDAP

a contunuación mostraré una serie de ejemplos con los que podremos modificar nuestro directorio.

### ldapadd

```bash
ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f profesores.ldif
```
este comando se utiliza para agregar nuevos registros al directorio LDAP. se usa un archivo de entrada en formato LDIF (LDAP Data Interchange Format) para proporcionar los datos.

- D: especifica el usuario administrador que tiene permisos para agregar entradas.
- w: especifica la contraseña del usuario administrador.
- f: especifica el archivo LDIF con los datos a agregar.

aqui un ejemplo de dicho "profesores.ldif".
```bash
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

### ldapmodify

este comando se utiliza para modificar registros existentes en el directorio LDAP. al igual que el comando anterior, se utiliza un archivo LDIF para describir los cambios.

```bash
ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif
```

aqui un ejemplo de dicho "cambiar_usuario.ldif".
```bash
# Cambiar contraseña Usuario
dn: uid=alu01,ou=usuarios,dc=proyecto-empresa,dc=local
changetype: modify
replace: userPassword
userPassword: 654321
```

## autenticacion web con ldap

podemos configurar un servidor web para pedirnos credenciales respecto a uno o un grupo de usuarios en nuestro directorio ldap. esto lo podemos hacer con apache2 y con nginx.

### apache2

primero que todo, para usar la autenticacion a través de ldap en apache2 deberemos habilitar el modulo para ello. con el siguiente comando podemos hacerlo.

```bash
sudo a2enmod authnz_ldap
```

entonces deberemos modificar nuestro archivo de configuracion de web para implementar la autenticación. aqui dejo un ejemplo de archivo ".conf":

```xml
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
ahora explico brevemente que significan algunas de las declaraciones del ejemplo:

- AuthType Basic: define que hay autenticacion.
- AuthBasicProvider ldap: define que se usará ldap para la autenticacion.
- AuthLDAPURL: define la url del server ldap.
- AuthLDAPBindDN : especifica el usuario.
- Require valid-user: obliga a usar un usuario valido.

para aplicar los cambios deberemos de reiniciar apache2.

```bash
sudo systemctl restart apache2
```

## nginx

en nginx deberemos primero configurar nginx en si para poder habilitar esta funcion. aqui tenemos el archivo "/etc/nginx/nginx.conf": 

```bash
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

obviamente deberiamos cambiar la url y los binds para que cuandren con nuestro server y con nuestros usuarios autenticables.

entonces configuramos nuestro sitio para habilitar la autenticacion. esta configuración es generica asique no hacen falta cambios.

```bash
server {
    listen 80;
    server_name sitioldap;
    root /var/www/sitioldap;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
        auth_ldap "Protected Area";
        auth_ldap_servers mi_servidor_ldap;
    }
}
```
