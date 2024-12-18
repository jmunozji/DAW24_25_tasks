---
title: 'CH4 - PPE'
---

# **Cheatsheet UD4 - Servicios de Red**

> Este documento resume los comandos y ficheros de configuración para la creación y gestión de servicios de red **DNS** y **LDAP** en sistemas Ubuntu, correspondientes a la **Unidad 4** del curso de **Despliegue de Aplicaciones**.




## 🖥️ Servidor DNS

### 🌐 Herramientas de resolución de nombres

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt update && sudo apt upgrade`              | Actualiza la lista y los paquetes instalados a la última versión disponible.                                 |
| `sudo apt install dnsutils`                        | Instalamos las herramientas en nuestro sistema.                                                              |
| `cat /etc/resolv.conf \| grep nameserver`          | Visualizar la IP del servidor DNS que tenemos configurado.                                                   |
| `host DOM_NAME 127.0.0.1`                          | Devuelve información básica sobre el dominio, como la dirección IP. Si no le especificamos la IP, coge la del servidor DNS configurado por defecto.                |
| `dig DOM_NAME`                                     | Proporciona detalles sobre la resolución DNS, incluyendo secciones de respuesta, autoridad y adicionales.    |
| `nslookup DOM_NAME`                                | Permite realizar consultas específicas y obtener información detallada sobre el dominio.                     |

### 🌐 Instalación del servidor DNS

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt install bind9 bind9utils bind9-doc`      | Instalamos los repositorios oficiales para configurar el servidor.                                           |

Una vez lo hemos instalado, vamos a editar el fichero `/etc/systemd/resolved.conf` y modificamos la siguiente línea para establecer nuestro servidor como DNS por defecto.

```ini
[Resolve]
DNS=127.0.0.1
```

> Al terminar, debemos reiniciar el servicio para aplicar la configuración `sudo systemctl restart systemd-resolved`.

### 🌐 Configuraciones DNS

| **🔧 Fichero de configuración**                    | **📝 Funcionalidad**                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `/etc/bind/named.conf`                             | Archivo principal que incluye otros archivos de configuración y define opciones globales.                    |
| `/etc/bind/named.conf.options`                     | Contiene las opciones globales, las direcciones de los servidores DNS a los que reenviar consultas.          |
| `/etc/bind/named.conf.local`                       | Define zonas locales, zonas directas e inversas para los dominios que el servidor DNS es autoritativo.       |
| `/etc/bind/named.conf.default-zones`               | Contiene la configuración de las zonas predeterminadas, las zonas raíz y localhost.                          |

> **💡 Tip**: Es recomendable hacer una copia de seguridad de cada fichero de configuración antes de hacer cualquier tipo de modificación. Ejemplo: `sudo cp /etc/bind/named.conf.options /etc/bind/named.conf.options.old`. Además, cada vez que queramos aplicar los cambios, debemos reiniciar el servicio con el comando `sudo systemctl restart bind9`.

### 🛠️ Configuración named.conf.options

```conf
options {
    directory "/var/cache/bind";

    # Permitir consultas desde cualquier origen
    allow-query { any; };

    # Desactivar la recursividad
    recursion no;

    # Configuración de los forwarders (DNS de Google)
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };

    dnssec-validation auto;
    listen-on-v6 { any; };
};
```

> Para verificar que el fichero no tiene errores de sintaxis, podemos ejecutar el comando `sudo named-checkconf`.

### 🛠️ Configuración named.conf.local

```conf
# Zona directa
zone "ZONE_NAME" {
        type master;
        file "/etc/bind/db.ZONE_NAME"; #Ruta donde ubicamos nuestro archivo de zona
};

# Zona inversa
zone "104.85.3.in-addr.arpa" {
    type master;
    file "/etc/bind/db.3.85.104";  # Ruta al archivo de zona inversa
};
```

Una vez configuramos las zonas en el fichero, debemos crear los archivos de zona correspondientes con la ruta que hemos establecido en `named.conf.local`.

### 🛠️ Configuración de la zona directa

```dns
; Zona directa (/etc/bind/db.ZONE_NAME)
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

### 🛠️ Configuración de la zona inversa

```dns
; Zona inversa (/etc/bind/db.3.85.104)
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

> Para verificar que el archivo de zona no tiene errores de sintaxis, podemos ejecutar el comando `sudo named-checkzone ZONE_NAME /etc/bind/db.ZONE_NAME`.

### 🔄 Reiniciar el servicio

Después de realizar las configuraciones, reinicia el servicio BIND9 para aplicar los cambios:

```bash
sudo systemctl restart bind9
```

Con estas configuraciones y verificaciones, tu servidor DNS debería estar funcionando correctamente y respondiendo a las consultas tanto locales como externas.




## 🖥️ Servidor LDAP

### 🛠️ Instalación del servicio

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt install slapd ldap-utils`                | Instala el servidor OpenLDAP y sus utilidades.                                                               |
| `sudo dpkg-reconfigure slapd`                      | Reconfigura el servidor LDAP (slapd) permitiendo cambiar la configuración inicial.                           |
| `sudo slapcat`                                     | Ver la información actual de la base de datos de ldap desde terminal.                                        |

### 🖥️ Configuración mediante phpLDAPadmin

| **🔧 Comando**                                     | **📝 Descripción**                                                                                           |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| `sudo apt install phpldapadmin`                    | Actualiza la lista y los paquetes instalados a la última versión disponible.                                 |

> **💡 Tip**: Antes de acceder a la herramienta, es recomendable acceder al fichero de configuración `/etc/phpldapadmin/config.php` y cambiar el DIT por defecto por el que vamos a utilizar nosotros. Por ejemplo:

```diff
-   $servers->;setValue('server','base',array('dc=example,dc=com'));
-   $servers->setValue('login','bind_id','cn=admin,dc=example,dc=com');

+   $servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
+   $servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
```

### 🗂️ Configuración mediante ficheros ldif

```ldif
# Ejemplo de estructura básica de fichero ldif

# OU
dn: ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: usuarios

# Grupos
dn: cn=profesores,ou=grupos,dc=proyecto-empresa,dc=local
objectClass: posixGroup
cn: profesores
gidNumber: 10000 # es importante que no lo esté utilizando otro grupo antes

# Usuarios
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

| **🔧 Comando**                                                | **📝 Descripción**                                                                                |
|---------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `ldapadd -x -D cn=admin,dc=USER -w PASSWD -f FILE.ldif`       | Añade entradas al directorio LDAP desde un archivo LDIF.                                          |
| `ldapmodify -x -D cn=admin,dc=USER -w PASSWD -f FILE.ldif`    | Modifica entradas existentes en el directorio LDAP utilizando un archivo LDIF.                    |
| `ldapsearch -x -b dc=DN "(&(uid=*profe*)(gidNumber=10001))"`  | Realiza una busqueda en el directorio LDAP con el filtro especificado entre comas "".             |
| `ldapdelete -x -D cn=admin,dc=USER -w PASSWD uid=USER,ou=OU`  | Elimina un usuario del directorio LDAP.                                                           |

### 🌐 Autenticación con Apache

Para habilitar la autenticación, debemos realizar los cambios en los siguientes ficheros de configuración de **Apache** y **LDAP**:

```diff
# /etc/apache2/sites-enabled/000-default.conf

<Directory "/var/www/html/ldap"> 
        AuthType Basic
        AuthName "Apache LDAP authentication"
        AuthBasicProvider ldap 
+        AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub" 
+        AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
+        AuthLDAPBindPassword ieselcaminas
        Require valid-user
</Directory>
```

```diff
# /etc/ldap/ldap.conf

+    BINDDN cn=admin,dc=daw,dc=ieselcaminas
+    BINDPW ieselcaminas
```

> **💡 Tip**: Debemos asegurarnos también de que el módulo de autenticación de LDAP está habilitado en Apache con `sudo a2enmod authnz_ldap`.
