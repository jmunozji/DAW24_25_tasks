---
title: CH4 - ALS
---

# Ultimate Cheat Sheet: Servicios de Red (DNS y LDAP)

Esta referencia definitiva cubre conceptos clave, configuraciones, y comandos esenciales para la administración de los servicios DNS y LDAP.

---

## 1. **DNS (Domain Name System)**
El **DNS** traduce nombres de dominio a direcciones IP, facilitando la navegación en redes.

### Conceptos Clave
- **Resolución de nombres**: Traduce dominios a IPs.
- **Jerarquía**: Estructura en árbol invertido.
- **Zonas**: Segmentos del espacio de nombres administrados por servidores.
- **Registros de Recursos (RR)**:
  - **SOA**: Información de la zona.
  - **NS**: Servidores de nombres autorizados.
  - **A/AAAA**: Asociación de dominios con direcciones IPv4/IPv6.
  - **CNAME**: Alias de dominios.
  - **MX**: Servidores de correo.
  - **PTR**: Resolución inversa (IP -> dominio).
  - **TXT**: Información adicional (e.g., claves DKIM).

### Archivos Clave
| Archivo                          | Descripción                                     |
|----------------------------------|-------------------------------------------------|
| `/etc/bind/named.conf`           | Archivo principal de configuración.            |
| `/etc/bind/named.conf.options`   | Opciones globales.                             |
| `/etc/bind/named.conf.local`     | Definición de zonas locales.                   |
| `/etc/bind/db.example.com`       | Archivo de zona para registros DNS.            |

### Ejemplo de Configuración de Zona
Archivo `/etc/bind/db.example.com`:
```dns
$TTL 1d
$ORIGIN example.com.
@   IN  SOA     ns1.example.com. admin.example.com. (
                  2024010101 ; Serial
                  8H         ; Refresh
                  2H         ; Retry
                  4W         ; Expire
                  1D )       ; Minimum TTL
    IN  NS      ns1.example.com.
ns1 IN  A       192.168.1.1
www IN  A       192.168.1.2
```

### Comandos Esenciales
| Comando                                 | Descripción                                      |
|-----------------------------------------|--------------------------------------------------|
| `sudo named-checkconf`                  | Verifica la sintaxis de configuración DNS.       |
| `sudo named-checkzone example.com /etc/bind/db.example.com` | Valida un archivo de zona.           |
| `host <dominio>`                        | Consulta rápida de registros DNS.               |
| `dig <dominio>`                         | Detalles avanzados de resolución DNS.           |
| `nslookup <dominio>`                    | Resolución interactiva/no interactiva.          |

---

## 2. **LDAP (Lightweight Directory Access Protocol)**
El **LDAP** permite gestionar información jerárquica sobre usuarios, grupos y recursos.

### Conceptos Clave
- **DIT (Directory Information Tree)**: Estructura jerárquica de datos.
- **DN (Distinguished Name)**: Identificador único de una entrada.
- **Autenticación y Autorización**: Facilita el control de acceso centralizado.

### Archivos Clave
| Archivo                     | Descripción                                     |
|-----------------------------|-------------------------------------------------|
| `/etc/ldap/ldap.conf`       | Configuración del cliente LDAP.                |
| `/etc/slapd/slapd.conf`     | Configuración del servidor LDAP.               |
| `estructura_basica.ldif`    | Archivo LDIF para inicializar la base de datos. |

### Ejemplo de Archivo LDIF
Estructura básica:
```ldif
dn: dc=example,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: Example Organization
dc: example

dn: ou=users,dc=example,dc=com
objectClass: organizationalUnit
ou: users

dn: uid=user01,ou=users,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
uid: user01
cn: User One
sn: One
loginShell: /bin/bash
uidNumber: 1001
gidNumber: 1001
homeDirectory: /home/user01
userPassword: {SSHA}encrypted_password
```

### Comandos Esenciales
| Comando                                    | Descripción                                              |
|--------------------------------------------|----------------------------------------------------------|
| `sudo dpkg-reconfigure slapd`              | Reconfigura el servidor LDAP.                           |
| `sudo slapcat`                             | Exporta la base de datos LDAP a formato LDIF.           |
| `ldapadd -x -D "cn=admin,dc=example,dc=com" -w <password> -f <archivo.ldif>` | Agrega entradas LDAP desde un archivo LDIF. |
| `ldapsearch -x -b "dc=example,dc=com" "(uid=user01)"` | Realiza búsquedas en la base LDAP.         |
| `ldapdelete -x -D "cn=admin,dc=example,dc=com" -w <password> <dn>` | Elimina entradas LDAP.                |

### Modificar Contraseñas
Ejemplo de archivo LDIF para cambiar contraseña:
```ldif
dn: uid=user01,ou=users,dc=example,dc=com
changetype: modify
replace: userPassword
userPassword: nuevaContraseña
```
Comando:
```bash
ldapmodify -x -D "cn=admin,dc=example,dc=com" -w <password> -f modificar.ldif
```

---

## 3. **Autenticación LDAP en Servidores Web**

### Apache
1. Habilitar módulos:
```bash
sudo a2enmod authnz_ldap
sudo systemctl restart apache2
```
2. Configurar VirtualHost:
```apache
<VirtualHost *:80>
    ServerName example.com
    <Directory /var/www/html/protegido>
        AuthType Basic
        AuthName "Autenticación LDAP"
        AuthBasicProvider ldap
        AuthLDAPURL "ldap://ldap.example.com:389/ou=users,dc=example,dc=com?uid"
        AuthLDAPBindDN "cn=admin,dc=example,dc=com"
        AuthLDAPBindPassword "adminPassword"
        Require valid-user
    </Directory>
</VirtualHost>
```

### NGINX
1. Descargar e instalar el módulo LDAP:
```bash
wget https://example.com/nginx-auth-ldap.deb
sudo dpkg -i nginx-auth-ldap.deb
```
2. Configurar nginx.conf:
```nginx
http {
    ldap_server example_ldap {
        url ldap://ldap.example.com/ou=users,dc=example,dc=com?uid?sub;
        binddn "cn=admin,dc=example,dc=com";
        binddn_passwd "adminPassword";
        group_attribute member;
        group_attribute_is_dn on;
        require valid_user;
    }
    include /etc/nginx/sites-enabled/*;
}
```
3. Configurar el sitio:
```nginx
server {
    listen 80;
    server_name example.com;
    location / {
        auth_ldap "Protected Area";
        auth_ldap_servers example_ldap;
    }
}
```

---


