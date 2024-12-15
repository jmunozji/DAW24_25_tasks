---
title: 'CH4 - MNOG'
---
# Cheat Sheet: Servicios de Red - DNS y LDAP

---

## DNS (Domain Name System)
Sistema que traduce nombres de dominio legibles por humanos (ej. www.ejemplo.com) a direcciones IP numéricas (ej. 192.0.2.1).

### Conceptos Clave
- **Función**
    - Resolución de Nombres: Permite a los usuarios acceder a recursos en Internet utilizando nombres fáciles de recordar en lugar de direcciones IP.
    - Directorio Distribuido: Actúa como un directorio que almacena información sobre nombres de dominio y sus correspondientes direcciones IP.
    - Redundancia y Balanceo de Carga: Permite que un dominio apunte a múltiples direcciones IP, mejorando la disponibilidad y el rendimiento.

- **Jerarquía**: Estructura en forma de árbol invertido.
- **Zonas**: Porciones del espacio de nombres administradas por un servidor autorizado.

### Tipos de Servidores DNS
- **Maestro (Primario)**: Gestiona archivos de zona editables.
- **Esclavo (Secundario)**: Copia de solo lectura del maestro.
- **Cache**: Almacena respuestas para mejorar tiempos de consulta.
- **Forwarder**: Reenvía consultas a otros servidores.
- **Solo autorizado**: Responde solo sobre sus zonas, no realiza recursividad.
- **Servidores raíz**: Autorizados para el dominio raíz (".").

### Tipos de Consultas
- **Recursiva**: El servidor entrega la respuesta completa.
- **Iterativa**: El servidor da referencias para seguir la consulta.

### Registros de Recursos (RR)
| **Tipo**  | **Descripción**                                                                 |
|-----------|---------------------------------------------------------------------------------|
| SOA     | Información de la zona (servidor principal, email del admin, etc.).            |
| NS      | Servidores autorizados para una zona.                                          |
| A       | Asocia un nombre de dominio con una dirección IPv4.                            |
| CNAME   | Define alias para un dominio.                                                  |
| MX      | Especifica servidores de correo.                                               |
| PTR     | Resolución inversa (IP -> dominio).                                            |
| TXT     | Información adicional (e.g., claves de seguridad).                             |

### Herramientas Útiles
- **nslookup**: Diagnóstico DNS.
- **dig**: Consultas avanzadas y flexibles.
- **host**: Consultas simples.
- **whois**: Información de registro de dominios.

---

## LDAP (Lightweight Directory Access Protocol)
Protocolo utilizado para acceder y mantener servicios de directorio que almacenan información sobre usuarios, dispositivos y recursos en una red.
### Conceptos Clave
- **Función**
    - Organización Jerárquica: Estructura de datos organizada en una jerarquía, facilitando la búsqueda y el acceso a la información.
    - Gestión Centralizada: Permite la administración centralizada de usuarios y recursos, simplificando la gestión de accesos y permisos.
    - Autenticación y Autorización: Facilita la autenticación de usuarios y la autorización de acceso a recursos en la red.

- **DIT (Directory Information Tree)**: Estructura jerárquica de entradas.
- **DN (Distinguished Name)**: Identificador único de una entrada.

### Atributos Comunes
| **Atributo** | **Descripción**                         |
|--------------|-----------------------------------------|
| uid        | Identificación única del usuario.       |
| cn         | Nombre común.                          |
| sn         | Apellido.                              |
| mail       | Correo electrónico.                    |
| objectClass| Tipo de objeto (persona, grupo, etc.).  |

### Formato LDIF
- Representación estándar en texto:

```ldif
dn: uid=usuario, ou=People, dc=ejemplo, dc=com
objectclass: account
cn: Usuario Ejemplo
mail: usuario@ejemplo.com
```
# Servicios de Red DNS y LDAP

## 1. Comandos de instalación

| **Comando**                                             | **Explicación**                                                      |
|---------------------------------------------------------|----------------------------------------------------------------------|
| `apt-get install dnsutils`                               | Comando para instalar herramientas dig, host y nslookup.             |
| `apt-get install bind9 bind9utils bind9-doc`             | Comando para instalar un servidor DNS en Debian.                     |
| `apt-get install slapd ldap-utils`                       | Comando para instalar un servidor OpenLDAP.                          |
| `sudo apt-get install phpldapadmin`                      | Comando para instalar Phpldapadmin.                                  |
| `sudo systemctl restart nginx`                           | Reiniciar el servicio de nginx.                                      |

## 2. Comandos de configuración y comprobación

| **Comando**                                            | **Explicación**                                                          |
|--------------------------------------------------------|--------------------------------------------------------------------------|
| `sudo named-checkzone daw.es /etc/bind/db.daw.es`      | Comando para comprobar que no hay errores en el archivo de creación de zona.|
| `nslookup 3.85.104.173`                                | Comando para consultar el nombre de dominio.                             |
| `dig -x 3.85.104.173`                                  | Comando para hacer una consulta inversa para obtener el nombre de dominio.|
| `sudo slapcat`                                         | Comando para ver la información actual de la base de datos LDAP.        |
| `sudo dpkg-reconfigure slapd`                          | Comando para configurar nuestro directorio LDAP.                       |

### Ejemplo de archivo de configuración del archivo `named.conf.local`:
```
zone "daw.es" {
    type master;
    file "/etc/bind/db.daw.es";  // Ruta donde ubicamos nuestro archivo de zona.
};
```

### Ejemplo de archivo de creación de zona (db.deaw.es):

```
$TTL 1d
$ORIGIN daw.es. ; base domain-name
@   IN  SOA     ns1.daw.es. admin.daw.es. (
                  2023112301  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
; Name Servers
    IN  NS      ns1.daw.es.
; A records
ns1 IN  A       3.85.104.173
www IN  A       3.85.104.173
```

### Ejemplo de archivo de configuración de PHP (phpLDAPadmin):

```phpr
$servers->setValue('server', 'base', array('dc=daw,dc=ieselcaminas'));
$servers->setValue('login', 'bind_id', 'cn=admin,dc=daw,dc=ieselcaminas');
```

## 3. Comandos de LDAP

| **Comando**                                                                                                                 | **Explicación**                                                            |
|-----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| `ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f estructura_basica.ldif`                             | Comando para añadir unidades organizativas, usuarios o grupos a partir de un archivo LDIF. |
| `ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif`                            | Comando para modificar un parámetro de unidades organizativas, usuarios o grupos a partir de un archivo LDIF. |
| `ldapsearch -x -b dc=proyecto-empresa,dc=local "(uid=*profe*)"`                                                             | Comando para buscar unidades organizativas, usuarios o grupos.           |
| `ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local` | Comando para borrar unidades organizativas, usuarios o grupos.           |

### Ejemplo de archivo de creación de unidades organizativas:
```ldif
# Usuarios
dn: ou=usuarios,dc=proyecto-empresa,dc=local
objectClass: organizationalUnit
ou: usuarios
```

### Ejemplo de archivo de creación de grupos:
```ldif
# Grupo profesores
dn: cn=profesores,ou=grupos,dc=proyecto-empresa,dc=local
objectClass: posixGroup
cn: profesores
gidNumber: 10000
```

### Ejemplo de archivo de creación de usuarios:
```ldif
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

## 4. Comandos de Autenticación Apache contra LDAP

| **Comando**                               | **Explicación**                                                        |
|-------------------------------------------|------------------------------------------------------------------------|
| `sudo a2enmod authnz_ldap`                | Comando para habilitar el módulo de autenticación LDAP en Apache.    |

### Ejemplo de archivo `.htaccess`:
```apache
AuthType Basic
AuthName "Apache LDAP authentication"
AuthBasicProvider ldap
AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub"
Require valid-user
```

### Ejemplo de archivo ldap.conf:
```
BINDDN cn=admin,dc=daw,dc=ieselcaminas
BINDPW ieselcaminas
```