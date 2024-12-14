---
title: 'CH4 - RPE'
---

# **Cheatsheet de Servicios de red DNS y LDAP 🌐🔐**

## **DNS - Domain Name System 🌍**

### **Comandos de resolución de nombres 🔎**

#### **Instalación de dnsutils 🛠️**

```bash
sudo apt-get install dnsutils
```

| **Comando**        | **Descripción**                                    |
|--------------------|--------------------------------------------------|
| `host <dominio>`   | Muestra información básica del dominio, principalmente su dirección IP  |
| `dig <dominio>`    | Muestra más información sobre el dominio, como la lista de servidores DNS que utilizan para resolver el nombre |
| `nslookup <dominio>` | Similar a hosts, pero permite cambiar el servidor DNS a utilizar |

### **Instalación de Bind9 🛠️**

```bash
sudo apt-get install bind9 bind9utils bind9-doc
```

### **Archivos de configuración 📂**

| **⚙️ Archivo**                         | **📝 Descripción**                                    |
|-------------------------------------|--------------------------------------------------|
| `/etc/bind/named.conf`              | Archivo principal de configuración de Bind.      |
| `/etc/bind/named.conf.options`      | Archivo de configuración global.                 |
| `/etc/bind/named.conf.local`        | Archivo de configuración para las zonas locales. |
| `/etc/bind/named.conf.default-zones`| Archivo de configuración de zonas predeterminadas. |
| `/etc/bind/db.<dominio>`              | Registros DNS para un dominio personalizado.     |

### **Comandos para comprobar la configuración de Bind 🛠️**

| **Comando**        | **Descripción**                                    |
|--------------------|--------------------------------------------------|
| `named-checkconf`   | Verifica la sintaxis de los archivos de configuración |
| `named-checkzone <dominio> /etc/bind/<dominio>`    | Verifica la configuración de un dominio específico |

### **Ejemplos de archivos de configuración de Bind 📑**

#### **Archivo de configuración principal (named.conf)**

```code
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";

allow-query { any; };  // Permitir todas las solicitudes desde cualquier IP
```

#### **Archivo de configuración global (named.conf.options)**

```code
options {
    recursion no; // Desactiva la recursividad
    forwarders {
        // DNS de Google
        8.8.8.8;
        8.8.4.4;
    };
};
```

#### **Archivo de configuración local (named.conf.local)**

```code
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "prueba" {
    type master;
    file "/etc/bind/db.prueba";
};
```

#### **Archivo de zona de prueba (db.prueba)**

```code
$TTL 1d
$ORIGIN deaw.es. ; base domain-name
@   IN  SOA     ns1.prueba. admin.prueba. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
; Name Servers
    IN  NS      ns1.prueba.
; A records
ns1 IN  A       3.85.104.173
www IN  A       3.85.104.173
```

##### **⚠️ Configuración de una zona inversa**

Para crear una zona inversa añadiremos la siguiente línea al archivo de configuración de Bind:

```code
zone "104.85.3.in-addr.arpa" {
    type master;
    file "/etc/bind/db.3.85.104";  # Ruta al archivo de zona inversa
};
```

Y en el archivo de zona inversa:

```code
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

## **LDAP - Lightweight Directory Access Protocol 🔑**

### **Instalación de LDAP 🛠️**

```bash
sudo apt-get install slapd ldap-utils
```

### **Archivos de configuración 📂**

| **⚙️ Archivo**                         | **📝 Descripción**                                    |
|-------------------------------------|--------------------------------------------------|
| `/etc/ldap/ldap.conf`               | Configuración de los clientes LDAP |
| `/etc/sldap/sldap.conf`       | Configuración del servidor LDAP |
| `estructura.lidf`        | Definición de la estructura de los datos LDAP |


### **Comandos de configuración de LDAP**

| **Comando**        | **Descripción**                                    |
|--------------------|--------------------------------------------------|
| `dpkg-reconfigure slapd` | Recofigura el servicio LDAP |
| `slapcat` | Extrae el contenido de la base de datos LDAP |

### **Ejemplos de archivos de configuración de LDAP 📑**

#### **Archivo de estructura de datos (estructura.ldif)**

```ldif
dn: dc=aplicacion,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: Aplicación
dc: aplicacion

dn: ou=frontend,dc=aplicacion,dc=local
objectClass: organizationalUnit
ou: frontend

dn: ou=backend,dc=aplicacion,dc=local
objectClass: organizationalUnit
ou: backend
```

### **Comandos LDAP 🛠️**

#### **Creación de objetos LDAP ➕**

Añade nuevos objetos al directorio LDAP desde un archivo LDIF.

```bash
ldapadd -x -D "cn=admin,dc=aplicacion,dc=local" -w "<contraseña_LDAP>" -f estructura.ldif
```
- El parámetro `-x` indica que se debe añadir el objeto sin confirmación.
- El parámetro `-D` indica el DN de la cuenta de administrador.
- El parámetro `-w` indica la contraseña de la cuenta de administrador.
- El parámetro `-f` indica el archivo LDIF que contiene los datos a añadir.

#### **Modificación de objetos LDAP ✏️**

```bash
ldapmodify -x -D "cn=admin,dc=aplicacion,dc=local" -w "<contraseña_LDAP>" -f estructura.ldif
```

#### **Eliminación de objetos LDAP ❌**

```bash
ldapdelete -x -D "cn=admin,dc=aplicacion,dc=local" -w "<contraseña_LDAP>" -f estructura.ldif
```

#### **Consulta de objetos LDAP 🔍**

```bash
ldapsearch -x -b dc=aplicacion,dc=local "(uid=*dev01*)"
```

- El parámetro `-b` indica el DN de la base de datos a consultar.