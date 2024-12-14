---

title: 'CH4 - VATC'

---

# 🌟 Servicios de Red (DNS y LDAP)

> ✍️ **Victor Alexandru Teleanu Covaci**

## **🌐 DNS - Domain Name System**

### **🔍 Herramientas de consulta de DNS**

- Con `sudo apt-get install dnsutils` instalamos herramientas para consultas DNS.

| **🛠️ Archivo**                      | **📝 Descripción**                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------|
| `nslookup www.deaw.es`           | Realiza una consulta DNS para obtener la dirección IP de www.deaw.es.                         |
| `nslookup www.deaw.es IP_SERVER` | Realiza una consulta DNS utilizando un servidor específico para obtener la IP de www.deaw.es. |
| `dig deaw.es`                    | Realiza una consulta DNS para obtener información detallada de la zona de deaw.es.            |

## **⚙️ Configuración del servidor**

- Con `sudo apt-get install bind9 bind9utils bind9-doc` instalamos el servidor DNS con los repositorios oficiales.

### **📂 Archivos importantes**

| **🛠️ Archivo**                          | **📝 Descripción**                                   |
|---------------------------------     |-------------------------------------------------  |
| `/etc/bind/named.conf`               | Configuración principal de BIND.                  |
| `/etc/bind/named.conf.options`       | Opciones globales de configuración de BIND.       |
| `/etc/bind/named.conf.local`         | Configuración local de zonas y servidores.        |
| `/etc/bind/named.conf.default-zones` | Zonas predeterminadas para resolución de nombres. |
| `/etc/bind/db.deaw.es`               | Base de datos de zona para deaw.es.               | 

### **📝 Configuración de *named.conf***

```bash
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
```

### **⚙️ Configuración de *named.conf.options***

- Una buena práctica es hacer una copia de seguridad de un archivo de configuración cada vez que vayamos a realizar algún cambio, para ello usamos `sudo cp /etc/bind/named.conf.options /etc/bind/named.conf.options.backup`

```bash
options {

    allow-query { any; }; # Permite consultas DNS desde cualquier dirección IP

    recursion no; # Desactiva la recursión en el servidor DNS

    forwarders {
        8.8.8.8; # Google DNS
        8.8.4.4; # Google DNS
    };
}
```

- Con `sudo named-checkconf` verificamos la sintaxis del archivo de configuración de BIND (named.conf).

### **🔧 Configuración de *named.conf.local***

```bash
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "deaw.es" {
        type master;
        file "/etc/bind/db.deaw.es";  # Ruta donde ubicamos nuestro archivo de zona
};
```

### **🗂️ Creación del archivo de zona**

```bash
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
};
```

- Con `sudo named-checkzone deaw.es /etc/bind/db.deaw.es` verificamos la validez del archivo de zona de deaw.es (/etc/bind/db.deaw.es).

### **🔄 Creación del archivo de zona para resolución inversa**

Añadimos estas lineas en /etc/bind/named.conf.local:

```bash
zone "104.85.3.in-addr.arpa" {
    type master;
    file "/etc/bind/db.3.85.104";  # Ruta al archivo de zona inversa
};
```

Y la configuración de la zona de resolución inversa la realizaremos en el fichero descrito "/etc/bind/db.3.85.104"

```bash
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

- Con `sudo named-checkzone 104.85.3.in-addr.arpa /etc/bind/db.3.85.104`

## **🌐 LDAP - Lightweight Directory Access Protocol**

### **💻 Instalación y configuración OpenLDAP**

- Con `sudo apt-get install slapd ldap-utils` instalamos el servidor LDAP (slapd) y herramientas de línea de comandos para interactuar con LDAP (ldap-utils).

### **🛠️ Comandos importantes**

| **🛠️ Comando**                          | **📝 Descripción**                                    |
|--------------------------------------|----------------------------------------------------|
| `sudo slapcat`                       | Exporta la base de datos de OpenLDAP.              |
| `sudo dpkg-reconfigure slapd`        |   Reconfigura el servicio LDAP en Debian/Ubuntu.   |
| `sudo apt-get install phpldapadmin`  |   Instala la interfaz web para administrar LDAP.   |

- Con `sudo nano /etc/phpldapadmin/config.php` configuraremos la configuración de phpldapadmin.

Modificando estas lineas conseguiremos que el DIT, cuando vayamos a "http://IPSERVER/phpldapadmin", sea el nuestro:

```bash
$servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
$servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
```

### **🆕 Creación de objetos con archivo .ldif**

Creación del archivo estructura_basica.ldif

```bash
# Usuarios
dn: ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: usuarios

# Grupos
dn: ou=grupos,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: grupos
```

Y para incorporar esta información en la base de datos usamos el siguiente comando:

```bash
ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f estructura_basica.ldif
```

### **🔧 Modificación de objetos**

Creación del archivo cambiar_usuario.ldif

```bash
# Cambiar contraseña Usuario
dn: uid=alu01,ou=usuarios,dc=proyecto-empresa,dc=local
changetype: modify
replace: userPassword
userPassword: 654321
```

Y lo cambiamos con el siguiente comando:

```bash
ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif
```

### **🔍 Búsqueda de objetos**

#### Para buscar a todos los usuarios:

```bash
ldapsearch -x -b dc=proyecto-empresa,dc=local "(uid=*usuarios*)"
```

#### Para buscar a un usuario en concreto:

```bash
ldapsearch -x -b dc=proyecto-empresa,dc=local "(uidNumber=1002)"
```

#### Para buscar a usuarios con gid 10001:

```bash
ldapsearch -x -b dc=proyecto-empresa,dc=local "(&(uid=*usuarios*)(gidNumber=10001))"
```

### **❌ Borrar registros**

Para borrar a un usuario en concreto (alu02):

```bash
$ ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local
```

#### **📚 Explicación de parámetros**

- `-x`: Desactiva la autenticación SASL, usa autenticación simple.
- `-D`: Especifica el DN del usuario con permisos.
- `-w`: Proporciona la contraseña del usuario.
- `-f`: Indica el archivo LDIF con los datos a agregar/modificar.
- `-b`: Define la base de búsqueda en el árbol LDAP.
