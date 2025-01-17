---
title: 'CH4 - ABM'
---

# 🛠️ **Cheatsheet: Servicios de Red (DNS y LDAP)** 🛠️

Este cheatsheet proporciona una referencia rápida y clara para los servicios de red **DNS** y **LDAP**, con una clasificación adecuada de comandos, archivos de configuración y opciones importantes. 

---

## 🧭 **1. DNS (Domain Name System)**
El **DNS** convierte nombres de dominio legibles por humanos (por ejemplo, `example.com`) en direcciones IP.

### 📂 **Archivos de Configuración Importantes**
| **Archivo**                     | **Descripción**                                   |
|---------------------------------|-------------------------------------------------|
| `/etc/bind/named.conf`           | Archivo principal de configuración de BIND. Incluye otros archivos de configuración. |
| `/etc/bind/named.conf.options`   | Configuración de opciones globales (por ejemplo, reenviadores). |
| `/etc/bind/named.conf.local`     | Definición de zonas locales personalizadas.      |
| `/etc/bind/named.conf.default-zones` | Define las zonas predeterminadas (por ejemplo, `localhost`). |
| `/etc/bind/db.deaw.es`           | Archivo de zona con los registros de recursos (SOA, NS, A, etc.). |

---

### 🔧 **Comandos para Administrar BIND**
| **Comando**                             | **Descripción**                                      |
|---------------------------------------|---------------------------------------------------|
| `sudo named-checkconf`                | Verifica la sintaxis de la configuración de BIND.  |
| `sudo named-checkzone deaw.es /etc/bind/db.deaw.es` | Verifica la sintaxis y la validez del archivo de la zona DNS. |
| `host <nombre_dominio>`               | Resuelve un nombre de dominio en una dirección IP. |
| `nslookup <nombre_dominio>`           | Resuelve un nombre de dominio, proporcionando más detalles. |

---

### 📜 **Ejemplo de Configuración de Zona (db.deaw.es)**
```dns
$TTL 1d
$ORIGIN deaw.es. ; dominio base
@   IN  SOA     ns1.deaw.es. admin.deaw.es. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
; Servidores de nombres (Name Servers)
    IN  NS      ns1.deaw.es.
; Registros A (direcciones IP)
ns1 IN  A       3.85.104.173
www IN  A       3.85.104.173
```

---

### ⚙️ **Configuración de /etc/bind/named.conf**
```conf
// Archivo de configuración principal de BIND
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";

allow-query { any; };  // Permitir consultas desde cualquier IP
```

---

### 📘 **Opciones Comunes de Configuración (named.conf.options)**
```conf
options {
    recursion no; // Desactiva la recursividad
    forwarders {
        8.8.8.8;  // Google DNS 1
        8.8.4.4;  // Google DNS 2
    };
};
```

---

## 🔐 **2. LDAP (Lightweight Directory Access Protocol)**
El **LDAP** se usa para almacenar información de usuarios, grupos y otros objetos de forma jerárquica.

### 📂 **Archivos de Configuración Importantes**
| **Archivo**                     | **Descripción**                                    |
|---------------------------------|--------------------------------------------------|
| `/etc/ldap/ldap.conf`             | Configuración principal de cliente LDAP.          |
| `/etc/slapd/slapd.conf`           | Configuración del servidor LDAP (slapd).         |
| `estructura_basica.ldif`          | Archivo LDIF con la estructura inicial de la base de datos LDAP. |

---

### 🔧 **Comandos para Administrar LDAP**
| **Comando**                                   | **Descripción**                                      |
|---------------------------------------------|-----------------------------------------------------|
| `sudo dpkg-reconfigure slapd`               | Reconfigurar el servicio slapd (LDAP).               |
| `sudo slapcat`                              | Exportar toda la base de datos LDAP en formato LDIF. |
| `ldapadd -x -D "cn=admin,dc=proyecto-empresa,dc=local" -w <contraseña> -f estructura_basica.ldif` | Añadir objetos a la base LDAP desde un archivo LDIF. |
| `ldapsearch -x -b "dc=proyecto-empresa,dc=local" "(uid=*profe*)"` | Buscar entradas LDAP que coincidan con la expresión `(uid=*profe*)`. |

---

### 📜 **Ejemplo de Archivo LDIF (estructura_basica.ldif)**
```ldif
dn: dc=proyecto-empresa,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: Proyecto Empresa
dc: proyecto-empresa

dn: ou=people,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: people

dn: ou=groups,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: groups
```

---

### 🔐 **Comando ldapadd**
```bash
ldapadd -x -D "cn=admin,dc=proyecto-empresa,dc=local" -w ieselcaminas -f estructura_basica.ldif
```
> **Explicación**:  
- `-x`: Usa autenticación simple.  
- `-D`: Define el usuario administrador LDAP (distinguished name, DN).  
- `-w`: Contraseña del usuario LDAP.  
- `-f`: Especifica el archivo LDIF a usar para la importación.

---

### 📘 **Buscar Entradas con ldapsearch**
```bash
ldapsearch -x -b "dc=proyecto-empresa,dc=local" "(uid=*profe*)"
```
> **Explicación**:  
- `-x`: Usa autenticación simple.  
- `-b`: Define la base de búsqueda (`dc=proyecto-empresa,dc=local`).  
- `(uid=*profe*)`: Expresión de búsqueda LDAP. Busca todas las entradas cuyo `uid` contenga "profe".  

