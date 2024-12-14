---
title: CH4 - DGD
---

# 🌐 Configuración y Uso de DNS y LDAP

## 🖧 Comandos Útiles para Servidor DNS

### Herramientas de Diagnóstico
| Comando                | Descripción                                       |
|------------------------|---------------------------------------------------|
| `host <dominio>`       | Consulta rápida de registros DNS.                 |
| `dig <dominio>`        | Muestra detalles avanzados de resolución.         |
| `nslookup <dominio>`   | Resuelve nombres interactiva o no interactivamente.|

**Ejemplo de uso:**
```bash
host cisco.com
dig cisco.com
nslookup cisco.com
```

## Configuración Básica del Servidor

- Archivo principal: /etc/bind/named.conf.

    - Incluye configuraciones con:

    ```plaintext
    include "/etc/bind/named.conf.options";
    include "/etc/bind/named.conf.local";
    include "/etc/bind/named.conf.default-zones";
    ```

- Permitir consultas externas:

```plaintext
allow-query { any; };
```

### Configuración de Zonas

- Declaración de zona directa:

```plaintext
zone "example.com" {
    type master;
    file "/etc/bind/db.example.com";
};
```

- Archivo de zona (/etc/bind/db.example.com):

```plaintext
$TTL 1d
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

- Comprobar configuración:

```bash
sudo named-checkzone example.com /etc/bind/db.example.com
```

### Resolución Inversa

- Zona inversa:

```plaintext
zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.1";
};
```

- Archivo de zona inversa (/etc/bind/db.192.168.1):

```plaintext
$TTL 1d
@   IN  SOA     ns1.example.com. admin.example.com. (
                2024010101 ; Serial
                8H         ; Refresh
                2H         ; Retry
                4W         ; Expire
                1D )       ; Minimum TTL
    IN  NS      ns1.example.com.
1   IN  PTR     ns1.example.com.
```

- Consultar resolución inversa:

```bash
dig -x 192.168.1.1
```

## 📂 Comandos Útiles para LDAP
### 📁 Gestión de Objetos con LDIF

|Comando|Descripción|
|-------|-----------|
|ldapadd -x -D <admin_DN> -w <password> -f <archivo.ldif>|Añadir objetos al directorio.|
|ldapmodify -x -D <admin_DN> -w <password> -f <archivo.ldif>|Modificar objetos existentes.|
|ldapdelete -x -D <admin_DN> -w <password> <DN>|Eliminar objetos del directorio.|

### Ejemplo de archivo LDIF para usuarios:

```plaintext
dn: uid=profe01,ou=usuarios,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
uid: profe01
cn: Profesor01
sn: Departamento01
uidNumber: 1001
gidNumber: 10000
homeDirectory: /home/profe01
loginShell: /bin/bash
userPassword: {SSHA}contraseñaEncriptada
```

## 🔍 Consultas con LDAP

|Comando|Descripción|
|-------|-----------|
|ldapsearch -x -b <base_DN> <filtro>|Buscar en la base de datos.|

- Filtros comunes:

    - Buscar usuarios con "profe" en su UID:
    ```bash
    ldapsearch -x -b dc=example,dc=com "(uid=*profe*)"
    ```

    - Buscar usuarios con GID 10001:

    ```bash
    ldapsearch -x -b dc=example,dc=com "(gidNumber=10001)"
    ```

## 📜 Modificaciones

- Cambiar contraseña de un usuario:

    - Archivo LDIF:

    ```plaintext
    dn: uid=profe01,ou=usuarios,dc=example,dc=com
    changetype: modify
    replace: userPassword
    userPassword: nuevaContraseña
    ```

    - Comando:

    ```bash
    ldapmodify -x -D cn=admin,dc=example,dc=com -w adminPassword -f modificar.ldif
    ```

## 🔒 Autenticación con LDAP en Apache

### Configuración de Apache para LDAP

1. **Habilitar módulos necesarios:**
    ```bash
    sudo a2enmod authnz_ldap
    sudo systemctl restart apache2
    ```
2. **Configurar el archivo de VirtualHost:**
    ```plaintext
    <VirtualHost *:80>
    ServerName example.com

        <Directory /var/www/html/protegido>
            AuthType Basic
            AuthName "Autenticación LDAP"
            AuthBasicProvider ldap
            AuthLDAPURL "ldap://ldap.example.com:389/ou=usuarios,dc=example,dc=com?uid"
            AuthLDAPBindDN "cn=admin,dc=example,dc=com"
            AuthLDAPBindPassword "adminPassword"
            Require valid-user
        </Directory>
    </VirtualHost>
    ```

## 🔒 Autenticación con LDAP en NGINX

### Configuración de NGINX para LDAP

1. **Habilitar módulos necesarios:**
    
    Primero es necesario descargar los módulos, ya que Nginx por defecto no los tiene:

    ```bash
    wget https://jmunozji.github.io/DAW/Ud4%20Servicios%20de%20red%20DNS%20y%20LDAP/P5/libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb

    sudo dpkg -i libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb
    ```
2. **Configurar los archivos necesarios:**

    - Modificamos nginx.conf:

    ```plaintext
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

    - Añadimos la configuración al sitio:

    ```plaintext
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
