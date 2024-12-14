---
title: 'Ch4 - DMP'
---
# 📝 UD4 - Servicios de red DNS y LDAP - Cheatsheet

> David Maratrat Pons

# 🧑‍💻 Bind9

| Comando                                                       | Descripción                                         |
|---------------------------------------------------------------|-----------------------------------------------------|
| `sudo apt-get install bind9 bind9utils bind9-doc`             | `Instala el servicio DNS de bind9`                  |
| `named-checkconf`                                             | `Comprueba la sintaxis de los archivos .conf`       |
| `named-checkzone davidmaratrat.local db.davidmaratrat.local`  | `Comprueba la existencia del dominio en el archivo` |
---
### 🛠️ Configuración Bind9
- #### named.conf
    ```
    include "/etc/bind/named.conf.options";
    include "/etc/bind/named.conf.local";
    ```
- #### named.conf.local
    ```
    zone "davidmaratrat.local" {
    type master;
    file "/etc/bind/db.davidmaratrat.local";  // Archivo de zona para la zona local
    };
    
    zone "0.0.127.in-addr.arpa" {
    type master;
    file "/etc/bind/db.127";  // Configuración para resolución inversa (opcional)
    };
    ```
- #### named.conf.options
    ```
    options {
    directory "/var/cache/bind";  // Directorio de almacenamiento de cache de DNS

    // Configuración básica para permitir consultas de cualquier fuente
    allow-query { any; };

    // Configuración de reenvío a servidores DNS públicos
    forwarders {
        8.8.8.8;  // Servidor DNS de Google
        8.8.4.4;  // Otro servidor DNS de Google
    };

    dnssec-validation auto;
    
    listen-on { any; };  // Escuchar en todas las interfaces
    listen-on-v6 { any; };  // Escuchar en IPv6
    };
    ```
- #### db.davidmaratrat.local
    ```
    $TTL    604800
    $ORIGIN davidmaratrat.local.
    @       IN      SOA     davidmaratrat.local. admin.davidmaratrat.local. (
    2024121201 ; Serial
    604800     ; Refresh
    86400      ; Retry
    2419200    ; Expire
    604800 )   ; Negative Cache TTL
    ;
    
           IN      NS      practicaservicios.davidmaratrat.local.
    
    practicaservicios.davidmaratrat.local. IN A 127.0.0.1
    ldap.davidmaratrat.local.              IN CNAME practicaservicios.davidmaratrat.local.
    ```
- #### db.127
  ```
  $TTL 1d
  $ORIGIN 0.0.127.IN-ADDR.ARPA.
  @   IN  SOA     ns1.davidmaratrat.local. admin.davidmaratrat.local. (
                  2023121201  ; Serial
                  8H          ; Refresh
                  2H          ; Retry
                  4W          ; Expire
                  1D )        ; Minimum TTL
  
  ; Name Servers
  IN  NS      ns1.davidmaratrat.local.

  ; PTR record
  1 IN  PTR     ns1.davidmaratrat.local. ; fully qualified domain name (FQDN)
  ```

- #### 🐳 Dockerización de un BIND9
    ```
    docker run -d 
        --name=docker-dns 
        --publish 53:53/udp 
        --publish 53:53/tcp 
        internetsystemsconsortium/bind9:9.18
    ```

---

---

# 🧑‍💻 LDAP

| Comando                                 | Descripción                                                                   |
|-----------------------------------------|-------------------------------------------------------------------------------|
| `sudo apt-get install slapd ldap-utils` | `Instala el servicio LDAP`                                                    |
| `sudo dpkg-reconfigure slapd`           | `Configura el servicio una vez instalado`                                     |
| `sudo apt-get install phpldapadmin`     | `Instala la herramienta web para poder configurar el LDAP de manera sencilla` |
---
### 🛠️ Configuración LDAP
- #### 🐘 PHPLDAPADMIN
    - Para poder iniciar sesión de manera mas fácil, hay que añadir estas dos líneas al fichero /etc/phpldapadmin/config.php
        ```
        $servers->setValue('server','base',array('dc=davidmaratrat,dc=local'));
        $servers->setValue('login','bind_id','cn=admin,dc=davidmaratrat,dc=local');
        ```
    - Para crear usurarios se ha de usar las opciones de Default -> posixAccount + Account.
    - Para crear grupos se ha de usar la opción de Default -> posixGroup

- #### 📂 LDIF
    | Comando                                                                                                                    | Descripción                                           |
    |----------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
    | `ldapadd -x -D cn=admin,dc=davidmaratrat,dc=local -w davidmaratrat -f fichero.ldif`                                        | `Inyecta la información de los ldif en el LDAP`       |
    | `ldapmodify -x -D cn=admin,dc=davidmaratrat,dc=local -w davidmaratrat -f fichero.ldif `                                    | `Modifica una entidad con el fichero ldif en el LDAP` |
    | `ldapsearch -x -b dc=davidmaratrat,dc=local "(uid=*usuario01*)"`                                                           | `Busca una entidad con el fichero ldif en el LDAP`    |
    | `ldapdelete -x -D cn=admin,dc=davidmaratrat,dc=local -w davidmaratrat uid=usuario01,ou=usuarios,dc=davidmaratrat,dc=local` | `Elimina una entidad con el fichero ldif en el LDAP`  |

    - ##### 📜 Estructura-basica.ldif
      ```
      # Usuarios
      dn: ou=usuarios,dc=davidmaratrat,dc=local
      objectClass: organizationalUnit
      ou: usuarios
      ```
    - ##### 📜 Grupos.lidf
      ```
      #Grupo usuarios
      dn: cn=usuarios,ou=grupos,dc=davidmaratrat,dc=local
      objectClass: posixGroup
      cn: usuarios
      gidNumber: 10000
      ```
    - ##### 📜 Usuarios.lidf
      ```
      # Profe01
      dn: uid=usuario01,ou=usuarios,dc=davidmaratrat,dc=local
      objectClass: Account
      objectClass: posixAccount
      uid: usuario01
      cn: usuario01
      loginShell: /bin/bash
      uidNumber: 1001
      gidNumber: 10000
      homeDirectory: /home/usuario01
      userPassword: usuario01
      ```
    - ##### 📜 Modificación.lidf
      ```
      # Cambiar contraseña Usuario
      dn: uid=usuario01,ou=usuarios,dc=davidmaratrat,dc=local
      changetype: modify
      replace: userPassword
      userPassword: 123456
      ```
- #### 🐳 Dockerización de un LDAP
    ```
    docker run 
    --name docker-ldap
    --env LDAP_ORGANISATION="davidmaratrat.local" 
    --env LDAP_DOMAIN="davidmaratrat.local" 
    --env LDAP_ADMIN_PASSWORD="davidmaratrat" 
    --detach osixia/openldap:1.5.0
    ```
  
---

---

### 🐯 APACHE AUTENTICADO
| Comando                             | Descripción                                   |
|-------------------------------------|-----------------------------------------------|
| `sudo a2enmod authnz_ldap`          | `Activa el módulo de autenticación en apache` |

 - ### 📜 000-default.conf
    ```
    <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/html/test"> 
        AllowOverride All
        </Directory>
    </VirtualHost>
    ```
 - ### 📜 .htacces
    ```
    AuthType Basic
    AuthName "Apache LDAP authentication"
    AuthBasicProvider ldap
    AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=davidmaratrat,dc=local?uid?sub"
    AuthLDAPBindDN "cn=admin,dc=davidmaratrat,dc=local"
    AuthLDAPBindPassword davidmaratrat
    Require valid-user
    ```

## 🔧 Herramientas útiles
| Comando                               | Descripción                                                          |
|---------------------------------------|----------------------------------------------------------------------|
| `nslookup <dominio>`                  | `Realiza una consulta de un dominio`                                 |
| `nslookup -type=<registro> <dominio>` | `Realiza una consulta al dominio  de un tipo de registro específico` |
| `nslookup <dominio> <DNS>`            | `Realiza una consulta con un servidor DNS específico`                |
| `dig <dominio>`                       | `Realiza una consulta de un dominio`                                 |
| `dig <dominio> <registro>`            | `Realiza una consulta al dominio de un tipo de registro específico`  |
| `dig @<DNS> <dominio`                 | `Realiza una consulta con un servidor DNS específico`                |
| `dig -x <IP>`                         | `Realiza una consulta inversa de una IP`                             |
| `host <dominio>`                      | `Realiza una consulta de un dominio`                                 |
| `host -t <registro> <dominio>`        | `Realiza una consulta al dominio de un tipo de registro específico`  |
| `host <IP>`                           | `Realiza una consulta inversa de una IP`                             |
| `whois <dominio>`                     | `Realiza una consulta de un dominio`                                 |
| `whois <IP>`                          | `Realiza una consulta inversa de una IP`                             |