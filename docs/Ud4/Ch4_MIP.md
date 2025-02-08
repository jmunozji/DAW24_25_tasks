---
title: 'Ch4 - MIP'
---

### Cheatsheet: Configuración de un Servidor DNS en Debian

#### 1. **Preparación del Entorno**
- **Eliminar entradas en `/etc/hosts`**: Asegúrate de que no haya entradas previas que puedan interferir con la resolución DNS.
- **Crear una instancia EC2 en AWS**:
    - Nombre: `P5ServidorDNS`.
    - Sistema operativo: Debian.
    - Grupo de seguridad: Abre los puertos necesarios para consultas DNS (puerto 53 UDP/TCP).
- **Asignar una IP elástica**:
    - Asigna una IP elástica en AWS y asíociala a la instancia EC2.

#### 2. **Instalación de Herramientas de Diagnóstico**
- Instala las herramientas `dig`, `host`, y `nslookup`:
  ```bash
  sudo apt-get install dnsutils
  ```
- Verifica el servidor DNS configurado:
  ```bash
  cat /etc/resolv.conf
  ```

#### 3. **Pruebas de Resolución de Nombres**
- **Usar `host`**:
  ```bash
  host cisco.com
  ```
- **Usar `dig`**:
  ```bash
  dig cisco.com
  ```
- **Usar `nslookup`**:
  ```bash
  nslookup cisco.com
  ```
- **Consultar a un DNS específico**:
  ```bash
  nslookup cisco.com 8.8.8.8
  ```

#### 4. **Instalación del Servidor DNS (BIND9)**
- Instala BIND9:
  ```bash
  sudo apt-get install bind9 bind9utils bind9-doc
  ```
- Verifica si el servicio está activo:
  ```bash
  sudo systemctl status bind9
  ```

#### 5. **Configuración Básica de BIND9**
- **Archivo de configuración principal**: `/etc/bind/named.conf`.
- **Habilitar consultas externas**:
    - Edita `/etc/bind/named.conf.options` y añade:
      ```bash
      allow-query { any; };
      ```
- **Desactivar recursividad** (opcional):
  ```bash
  recursion no;
  ```
- **Configurar forwarders**:
    - Edita `/etc/bind/named.conf.options` y añade:
      ```bash
      forwarders {
          8.8.8.8;
          8.8.4.4;
      };
      ```
- **Reiniciar el servicio**:
  ```bash
  sudo systemctl restart bind9
  ```

#### 6. **Configuración de Zonas DNS**
- **Zona directa**:
    - Edita `/etc/bind/named.conf.local` y añade:
      ```bash
      zone "deaw.es" {
          type master;
          file "/etc/bind/db.deaw.es";
      };
      ```
    - Crea el archivo de zona `/etc/bind/db.deaw.es`:
      ```bash
      $TTL 1d
      $ORIGIN deaw.es.
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
    - Verifica la configuración:
      ```bash
      sudo named-checkzone deaw.es /etc/bind/db.deaw.es
      ```
- **Zona inversa**:
    - Edita `/etc/bind/named.conf.local` y añade:
      ```bash
      zone "104.85.3.in-addr.arpa" {
          type master;
          file "/etc/bind/db.3.85.104";
      };
      ```
    - Crea el archivo de zona inversa `/etc/bind/db.3.85.104`:
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
      173 IN  PTR     ns1.deaw.es.
      ```
    - Verifica la configuración:
      ```bash
      sudo named-checkzone 104.85.3.in-addr.arpa /etc/bind/db.3.85.104
      ```

#### 7. **Pruebas de Funcionamiento**
- **Resolución directa**:
  ```bash
  nslookup www.deaw.es
  dig deaw.es
  ```
- **Resolución inversa**:
  ```bash
  nslookup 3.85.104.173
  dig -x 3.85.104.173
  ```

#### 8. **Comprobación de la Caché DNS**
- Realiza una consulta inicial y observa el tiempo de respuesta:
  ```bash
  dig cisco.com
  ```
- Realiza la misma consulta nuevamente y verifica que el tiempo de respuesta ha disminuido debido a la caché.

#### 9. **Reiniciar el Servicio DNS**
- Después de cada cambio en la configuración, reinicia el servicio:
  ```bash
  sudo systemctl restart bind9
  ```
### Cheatsheet: Dockerización de un Servidor DNS con BIND9

#### 1. **Preparación del Entorno**
- **Verificar puerto 53**: Asegúrate de que el puerto 53 no esté en uso por otro servicio (como `systemd-resolved`). Si es necesario, detén el servicio:
  ```bash
  sudo systemctl stop systemd-resolved
  ```

#### 2. **Creación del Contenedor Docker**
- Usa la imagen oficial de BIND9:
  ```bash
  docker run -d \
      --name=miserverdns \
      --publish 53:53/udp \
      --publish 53:53/tcp \
      internetsystemsconsortium/bind9:9.18
  ```
- **Explicación**:
    - `-d`: Ejecuta el contenedor en segundo plano (modo detached).
    - `--name=miserverdns`: Asigna un nombre al contenedor.
    - `--publish 53:53/udp` y `--publish 53:53/tcp`: Publica el puerto 53 (UDP y TCP) en el host.

#### 3. **Prueba Inicial del Servidor DNS**
- Verifica que el servidor DNS funciona correctamente:
  ```bash
  nslookup cisco.com 127.0.0.1
  ```

#### 4. **Preparación de Archivos de Configuración**
- Crea una carpeta en tu host para almacenar los archivos de configuración:
  ```bash
  mkdir miserverdns
  cd miserverdns
  ```
- Crea los siguientes archivos dentro de la carpeta:
    - **`named.conf`**:
      ```bash
      include "/etc/bind/named.conf.options";
      include "/etc/bind/named.conf.local";
      ```
    - **`named.conf.options`**: Configura las opciones de BIND9 (por ejemplo, `allow-query` y `forwarders`).
    - **`named.conf.local`**: Define las zonas DNS (directa e inversa).
    - **`db.deaw.es`**: Archivo de zona directa para el dominio `deaw.es`.
    - **`db.3.85.104`**: Archivo de zona inversa para la red `3.85.104`.

- **Ejemplo de `named.conf.local`**:
  ```bash
  zone "deaw.es" {
      type master;
      file "/etc/bind/db.deaw.es";
  };

  zone "104.85.3.in-addr.arpa" {
      type master;
      file "/etc/bind/db.3.85.104";
  };
  ```

- **Ejemplo de `db.deaw.es`**:
  ```bash
  $TTL 1d
  $ORIGIN deaw.es.
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

- **Ejemplo de `db.3.85.104`**:
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
  173 IN  PTR     ns1.deaw.es.
  ```

#### 5. **Copiar Archivos al Contenedor**
- Copia los archivos de configuración al contenedor:
  ```bash
  docker cp named.conf miserverdns:/etc/bind/
  docker cp named.conf.options miserverdns:/etc/bind/
  docker cp named.conf.local miserverdns:/etc/bind/
  docker cp db.deaw.es miserverdns:/etc/bind/
  docker cp db.3.85.104 miserverdns:/etc/bind/
  ```

#### 6. **Reiniciar el Contenedor**
- Reinicia el contenedor para aplicar los cambios:
  ```bash
  docker restart miserverdns
  ```

#### 7. **Verificación de la Configuración**
- Verifica la configuración con los siguientes comandos:
  ```bash
  docker exec miserverdns named-checkconf
  docker exec miserverdns named-checkzone deaw.es /etc/bind/db.deaw.es
  docker exec miserverdns named-checkzone 104.85.3.in-addr.arpa /etc/bind/db.3.85.104
  ```

#### 8. **Pruebas de Funcionamiento**
- **Resolución directa**:
  ```bash
  nslookup www.deaw.es 127.0.0.1
  dig deaw.es @127.0.0.1
  ```
- **Resolución inversa**:
  ```bash
  nslookup 3.85.104.173 127.0.0.1
  dig -x 3.85.104.173 @127.0.0.1
  ```

### Cheatsheet: Configuración de un Servidor LDAP con OpenLDAP y phpLDAPadmin

#### 1. **Creación de la Máquina Virtual en AWS**
- **Nombre de la instancia**: `ServidorLDAP`.
- **Sistema operativo**: Debian.
- **Grupo de seguridad**: Abre los puertos necesarios para LDAP (389 TCP) y phpLDAPadmin (80 TCP).
- **Actualización del sistema**:
  ```bash
  sudo apt-get update && sudo apt-get upgrade -y
  ```

#### 2. **Instalación de OpenLDAP**
- Instala los paquetes necesarios:
  ```bash
  sudo apt-get install slapd ldap-utils
  ```
- Durante la instalación, se solicitará una contraseña para el administrador de LDAP. Usa `ieselcaminas`.

#### 3. **Configuración Inicial de OpenLDAP**
- Reconfigura `slapd` para definir el dominio base:
  ```bash
  sudo dpkg-reconfigure slapd
  ```
- **Pasos durante la reconfiguración**:
    1. **Omitir configuración inicial**: Selecciona "No".
    2. **Nombre del dominio**: Introduce `daw.ieselcaminas`.
    3. **Nombre de la organización**: Introduce `daw.ieselcaminas`.
    4. **Contraseña de administrador**: Usa `ieselcaminas`.
    5. **Base de datos backend**: Selecciona `MDB`.
    6. **Eliminar base de datos al purgar**: Selecciona "No".
    7. **Mover la base de datos antigua**: Selecciona "Yes".

- Verifica la configuración con:
  ```bash
  sudo slapcat
  ```
  Deberías ver el nodo raíz `dc=daw,dc=ieselcaminas`.

#### 4. **Instalación de phpLDAPadmin**
- Instala phpLDAPadmin para la gestión gráfica:
  ```bash
  sudo apt-get install phpldapadmin
  ```
- Accede a phpLDAPadmin desde tu navegador:
  ```
  http://<IP_DEL_SERVIDOR>/phpldapadmin
  ```

#### 5. **Configuración de phpLDAPadmin**
- Edita el archivo de configuración para ajustar el dominio base:
  ```bash
  sudo nano /etc/phpldapadmin/config.php
  ```
- Busca y modifica las siguientes líneas:
  ```php
  $servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
  $servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
  ```
- Guarda y cierra el archivo.

#### 6. **Creación de Unidades Organizativas (OU)**
- **Accede a phpLDAPadmin**:
    - Usuario: `cn=admin,dc=daw,dc=ieselcaminas`.
    - Contraseña: `ieselcaminas`.
- **Crea las OU**:
    1. Haz clic en `dc=daw,dc=ieselcaminas`.
    2. Selecciona "Create a child entry".
    3. Elige "Generic: Organizational Unit".
    4. Crea las OU `ou=grupos` y `ou=usuarios`.

#### 7. **Creación de Grupos**
- **Crea el grupo `profesores`**:
    1. Selecciona `ou=grupos`.
    2. Haz clic en "Create a child entry".
    3. Elige "Default" y luego "posixGroup".
    4. Completa los campos:
        - **Group Name**: `profesores`.
        - **GID Number**: `10000`.
- **Crea el grupo `alumnos`**:
    - Sigue los mismos pasos, pero usa `alumnos` como nombre y `10001` como GID.

#### 8. **Creación de Usuarios**
- **Crea el usuario `profe01`**:
    1. Selecciona `ou=usuarios`.
    2. Haz clic en "Create a child entry".
    3. Elige "Default" y luego selecciona `account` y `posixAccount`.
    4. Completa los campos:
        - **First Name**: `profe01`.
        - **Last Name**: `profesor`.
        - **Common Name**: `profe01`.
        - **User ID**: `profe01`.
        - **Group ID**: `10000` (profesores).
        - **Home Directory**: `/home/profe01`.
        - **Password**: Establece una contraseña.
- **Crea los usuarios `alu01` y `alu02`**:
    - Sigue los mismos pasos, pero usa `alumnos` como grupo (GID `10001`).

#### 9. **Verificación de la Configuración**
- **Verifica los usuarios y grupos**:
    - Navega por el árbol LDAP en phpLDAPadmin para asegurarte de que los usuarios y grupos se han creado correctamente.
- **Prueba la autenticación**:
    - Usa `ldapsearch` para verificar que los usuarios y grupos son accesibles:
      ```bash
      ldapsearch -x -b "dc=daw,dc=ieselcaminas" -D "cn=admin,dc=daw,dc=ieselcaminas" -w "ieselcaminas"
      ```
### Cheatsheet: Carga de Datos en un Servidor LDAP mediante Ficheros LDIF

#### 1. **Creación de la Máquina Virtual en AWS**
- **Nombre de la instancia**: `ServidorLDAP2`.
- **Sistema operativo**: Debian.
- **Grupo de seguridad**: Usa el grupo de seguridad `ServidorLDAP` creado anteriormente.
- **Actualización del sistema**:
  ```bash
  sudo apt-get update && sudo apt-get upgrade -y
  ```

#### 2. **Instalación de OpenLDAP**
- Instala los paquetes necesarios:
  ```bash
  sudo apt-get install slapd ldap-utils
  ```
- Durante la instalación, se solicitará una contraseña para el administrador de LDAP. Usa `ieselcaminas`.

#### 3. **Configuración Inicial de OpenLDAP**
- Reconfigura `slapd` para definir el dominio base:
  ```bash
  sudo dpkg-reconfigure slapd
  ```
- **Pasos durante la reconfiguración**:
    1. **Omitir configuración inicial**: Selecciona "No".
    2. **Nombre del dominio**: Introduce `proyecto-empresa.local`.
    3. **Nombre de la organización**: Introduce `proyecto-empresa.local`.
    4. **Contraseña de administrador**: Usa `ieselcaminas`.
    5. **Base de datos backend**: Selecciona `MDB`.
    6. **Eliminar base de datos al purgar**: Selecciona "No".
    7. **Mover la base de datos antigua**: Selecciona "Yes".

- Verifica la configuración con:
  ```bash
  sudo slapcat
  ```
  Deberías ver el nodo raíz `dc=proyecto-empresa,dc=local`.

#### 4. **Creación de Unidades Organizativas (OU)**
- Crea un archivo `estructura_basica.ldif` con el siguiente contenido:
  ```ldif
  # Usuarios
  dn: ou=usuarios,dc=proyecto-empresa,dc=local
  objectClass: organizationalUnit
  ou: usuarios

  # Grupos
  dn: ou=grupos,dc=proyecto-empresa,dc=local
  objectClass: organizationalUnit
  ou: grupos
  ```
- Añade las OU al servidor LDAP:
  ```bash
  ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f estructura_basica.ldif
  ```

#### 5. **Creación de Grupos**
- Crea un archivo `grupos.ldif` con el siguiente contenido:
  ```ldif
  # Grupo profesores
  dn: cn=profesores,ou=grupos,dc=proyecto-empresa,dc=local
  objectClass: posixGroup
  cn: profesores
  gidNumber: 10000

  # Grupo alumnos
  dn: cn=alumnos,ou=grupos,dc=proyecto-empresa,dc=local
  objectClass: posixGroup
  cn: alumnos
  gidNumber: 10001
  ```
- Añade los grupos al servidor LDAP:
  ```bash
  ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f grupos.ldif
  ```

#### 6. **Creación de Usuarios**
- Crea un archivo `profesores.ldif` con el siguiente contenido:
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
- Añade los usuarios al servidor LDAP:
  ```bash
  ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f profesores.ldif
  ```

- **Crea usuarios alumnos**:
    - Sigue el mismo formato que `profesores.ldif`, pero usa `alu01` y `alu02` como nombres, `2001` y `2002` como `uidNumber`, y `10001` como `gidNumber`.

#### 7. **Modificación de Objetos**
- Crea un archivo `cambiar_usuario.ldif` para cambiar la contraseña de un usuario:
  ```ldif
  # Cambiar contraseña Usuario
  dn: uid=alu01,ou=usuarios,dc=proyecto-empresa,dc=local
  changetype: modify
  replace: userPassword
  userPassword: 654321
  ```
- Modifica la contraseña:
  ```bash
  ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif
  ```

#### 8. **Búsqueda en la Base de Datos**
- **Buscar todos los profesores**:
  ```bash
  ldapsearch -x -b dc=proyecto-empresa,dc=local "(uid=*profe*)"
  ```
- **Buscar un profesor por `uidNumber`**:
  ```bash
  ldapsearch -x -b dc=proyecto-empresa,dc=local "(uidNumber=1002)"
  ```
- **Buscar profesores con `gidNumber` específico**:
  ```bash
  ldapsearch -x -b dc=proyecto-empresa,dc=local "(&(uid=*profe*)(gidNumber=10001))"
  ```

#### 9. **Eliminación de Registros**
- **Eliminar un usuario**:
  ```bash
  ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local
  ```
### Cheatsheet: Dockerización de un Servidor LDAP con OpenLDAP y phpLDAPadmin

#### 1. **Creación de una Red Docker**
- Crea una red Docker para que los contenedores de OpenLDAP y phpLDAPadmin puedan comunicarse:
  ```bash
  docker network create ldap-network
  ```

#### 2. **Creación del Contenedor OpenLDAP**
- Usa la imagen oficial de OpenLDAP para crear el contenedor:
  ```bash
  docker run \
      --net ldap-network \
      --hostname ldap-service \
      --name ldap-service \
      --env LDAP_ORGANISATION="daw.ieselcaminas" \
      --env LDAP_DOMAIN="daw.ieselcaminas" \
      --env LDAP_ADMIN_PASSWORD="ieselcaminas" \
      --detach osixia/openldap
  ```
- **Explicación**:
    - `--net ldap-network`: Conecta el contenedor a la red `ldap-network`.
    - `--hostname ldap-service`: Asigna un nombre de host al contenedor dentro de la red.
    - `--env`: Define variables de entorno para configurar OpenLDAP.
    - `--detach`: Ejecuta el contenedor en segundo plano.

- **Verifica la creación de la base de datos**:
  ```bash
  docker exec ldap-service slapcat
  ```
  Deberías ver el nodo raíz `dc=daw,dc=ieselcaminas`.

#### 3. **Creación del Contenedor phpLDAPadmin**
- Usa la imagen oficial de phpLDAPadmin para crear el contenedor:
  ```bash
  docker run \
      --net ldap-network \
      --name phpldapadmin-service \
      --publish 443:443 \
      --hostname phpldapadmin-service \
      --env PHPLDAPADMIN_LDAP_HOSTS=ldap-service \
      --detach osixia/phpldapadmin
  ```
- **Explicación**:
    - `--net ldap-network`: Conecta el contenedor a la red `ldap-network`.
    - `--publish 443:443`: Expone el puerto 443 del contenedor para acceder a phpLDAPadmin.
    - `--env PHPLDAPADMIN_LDAP_HOSTS=ldap-service`: Indica a phpLDAPadmin que el servidor LDAP está en el host `ldap-service`.

#### 4. **Acceso a phpLDAPadmin**
- Accede a phpLDAPadmin desde tu navegador:
  ```
  https://<IP_DEL_SERVIDOR>
  ```
- **Credenciales de inicio de sesión**:
    - **Usuario**: `cn=admin,dc=daw,dc=ieselcaminas`.
    - **Contraseña**: `ieselcaminas`.

#### 5. **Configuración de la Base de Datos LDAP**
- **Creación de Unidades Organizativas (OU)**:
    - Usa phpLDAPadmin para crear las OU `ou=usuarios` y `ou=grupos` bajo `dc=daw,dc=ieselcaminas`.
- **Creación de Grupos**:
    - Crea los grupos `profesores` (GID `10000`) y `alumnos` (GID `10001`).
- **Creación de Usuarios**:
    - Crea usuarios como `profe01`, `profe02`, `alu01`, y `alu02` dentro de las OU correspondientes.

#### 6. **Verificación de la Configuración**
- **Verifica los usuarios y grupos**:
    - Navega por el árbol LDAP en phpLDAPadmin para asegurarte de que los usuarios y grupos se han creado correctamente.
- **Prueba la autenticación**:
    - Usa `ldapsearch` para verificar que los usuarios y grupos son accesibles:
      ```bash
      ldapsearch -x -b "dc=daw,dc=ieselcaminas" -D "cn=admin,dc=daw,dc=ieselcaminas" -w "ieselcaminas"
      ```
### Cheatsheet: Autenticación Apache contra LDAP

#### 1. **Preparación del Entorno**
- **Arranca la máquina `ServidorLDAP`**: Asegúrate de que el servidor LDAP esté funcionando.
- **Verifica el servicio Apache**: Comprueba que Apache está en ejecución:
  ```bash
  sudo systemctl status apache2
  ```

#### 2. **Habilitar el Módulo de Autenticación LDAP en Apache**
- Habilita el módulo `authnz_ldap`:
  ```bash
  sudo a2enmod authnz_ldap
  ```

#### 3. **Creación del Directorio Protegido**
- Crea un directorio `test` dentro de `/var/www/html`:
  ```bash
  sudo mkdir /var/www/html/test
  ```
- Crea un archivo `index.html` dentro del directorio:
  ```bash
  sudo nano /var/www/html/test/index.html
  ```
  Contenido de `index.html`:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>Sitio LDAP!</title>
  </head>
  <body>
      <h1>Bienvenido al sitio validado por LDAP!</h1>
  </body>
  </html>
  ```
- Cambia el propietario del directorio y su contenido a `www-data`:
  ```bash
  sudo chown -R www-data:www-data /var/www/html/test
  ```

#### 4. **Configuración de Apache para Autenticación LDAP**
- Haz una copia de seguridad del archivo de configuración de Apache:
  ```bash
  sudo cp /etc/apache2/sites-enabled/000-default.conf /etc/apache2/sites-enabled/000-default.conf.backup
  ```
- Edita el archivo de configuración:
  ```bash
  sudo nano /etc/apache2/sites-enabled/000-default.conf
  ```
- Añade las siguientes líneas dentro del bloque `<VirtualHost>`:
  ```apache
  <Directory "/var/www/html/test">
      AuthType Basic
      AuthName "Apache LDAP authentication"
      AuthBasicProvider ldap
      AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub"
      AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
      AuthLDAPBindPassword ieselcaminas
      Require valid-user
  </Directory>
  ```
- **Explicación de las directivas**:
    - `AuthType Basic`: Define el método de autenticación básica.
    - `AuthName`: Especifica el mensaje que se muestra en el cuadro de diálogo de autenticación.
    - `AuthBasicProvider ldap`: Indica que el proveedor de autenticación es LDAP.
    - `AuthLDAPURL`: Especifica la URL del servidor LDAP y los parámetros de búsqueda.
    - `AuthLDAPBindDN` y `AuthLDAPBindPassword`: Credenciales para autenticarse en el servidor LDAP.
    - `Require valid-user`: Permite el acceso solo a usuarios autenticados.

- **Reinicia Apache**:
  ```bash
  sudo systemctl restart apache2
  ```

#### 5. **Prueba de Autenticación**
- Abre una ventana privada en tu navegador y accede a:
  ```
  http://<IP_DEL_SERVIDOR>/test
  ```
- Introduce las credenciales de un usuario LDAP (por ejemplo, `profe01` y su contraseña).
- Verifica que el acceso es correcto y que se muestra la página `index.html`.

#### 6. **Configuración con `.htaccess` (Opcional)**
- **Recupera la configuración original de Apache**:
  ```bash
  sudo cp /etc/apache2/sites-enabled/000-default.conf.backup /etc/apache2/sites-enabled/000-default.conf
  ```
- Modifica el bloque `<Directory>` en el archivo de configuración:
  ```apache
  <Directory "/var/www/html/test">
      AllowOverride All
  </Directory>
  ```
- **Crea un archivo `.htaccess`** en `/var/www/html/test`:
  ```bash
  sudo nano /var/www/html/test/.htaccess
  ```
  Contenido de `.htaccess`:
  ```apache
  AuthType Basic
  AuthName "Apache LDAP authentication"
  AuthBasicProvider ldap
  AuthLDAPURL "ldap://127.0.0.1/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub"
  Require valid-user
  ```
- **Configura las credenciales de LDAP en `/etc/ldap/ldap.conf`**:
  ```bash
  sudo nano /etc/ldap/ldap.conf
  ```
  Contenido de `ldap.conf`:
  ```bash
  # Opciones de conexión
  BINDDN cn=admin,dc=daw,dc=ieselcaminas
  BINDPW ieselcaminas
  ```
- **Reinicia Apache**:
  ```bash
  sudo systemctl restart apache2
  ```
- **Prueba nuevamente el acceso** a `http://<IP_DEL_SERVIDOR>/test`.

### Cheatsheet: Autenticación Apache contra LDAP Dockerizada

#### 1. **Preparación del Entorno**
- **Asegúrate de tener el servidor OpenLDAP dockerizado funcionando**: Debe estar configurado con los usuarios creados en la "Práctica 5 - Dockerización de servidor LDAP".
- **Verifica la red Docker**: Asegúrate de que la red `ldap-network` esté creada y que los contenedores de OpenLDAP y phpLDAPadmin estén conectados a ella.

#### 2. **Creación del Dockerfile**
- Crea un archivo `Dockerfile` con el siguiente contenido:
  ```dockerfile
  FROM php:7-apache

  # Activamos el módulo LDAP de Apache
  RUN a2enmod authnz_ldap

  # Creamos el directorio test
  RUN mkdir -p /var/www/html/test

  # Copiamos los ficheros necesarios
  COPY index.html /var/www/html/test/index.html
  COPY 000-default.conf /etc/apache2/sites-enabled/000-default.conf
  ```

- **Archivo `index.html`**:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>Sitio LDAP!</title>
  </head>
  <body>
      <h1>Bienvenido al sitio validado por LDAP!</h1>
  </body>
  </html>
  ```

- **Archivo `000-default.conf`**:
  ```apache
  <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot /var/www/html
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined

      <Directory "/var/www/html/test">
          AuthType Basic
          AuthName "Apache LDAP authentication"
          AuthBasicProvider ldap
          AuthLDAPURL "ldap://ldap-service/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub"
          AuthLDAPBindDN "cn=admin,dc=daw,dc=ieselcaminas"
          AuthLDAPBindPassword ieselcaminas
          Require valid-user
      </Directory>
  </VirtualHost>
  ```

#### 3. **Construcción de la Imagen Docker**
- Construye la imagen Docker:
  ```bash
  docker build -t my-apache2 .
  ```

#### 4. **Ejecución del Contenedor Apache**
- Lanza el contenedor Apache conectado a la red `ldap-network`:
  ```bash
  docker run \
      --net ldap-network \
      --name apache2_server \
      -d \
      -p 80:80 \
      my-apache2
  ```

#### 5. **Prueba de Autenticación**
- Accede desde un navegador a:
  ```
  http://<IP_DEL_SERVIDOR>/test
  ```
- Introduce las credenciales de un usuario LDAP (por ejemplo, `profe01` y su contraseña).
- Verifica que el acceso es correcto y que se muestra la página `index.html`.

#### 6. **Configuración con Variables de Entorno (Opcional)**
- **Modifica el archivo `000-default.conf`** para usar variables de entorno:
  ```apache
  PassEnv LDAP_BIND_ON
  PassEnv LDAP_PASSWORD
  PassEnv LDAP_URL
  <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot /var/www/html
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined

      <Directory "/var/www/html/test">
          AuthType Basic
          AuthName "Apache LDAP authentication"
          AuthBasicProvider ldap
          AuthLDAPBindDN ${LDAP_BIND_ON}
          AuthLDAPBindPassword ${LDAP_PASSWORD}
          AuthLDAPURL ${LDAP_URL}
          Require valid-user
      </Directory>
  </VirtualHost>
  ```

- **Reconstruye la imagen Docker**:
  ```bash
  docker build -t my-apache2 .
  ```

- **Lanza el contenedor con variables de entorno**:
  ```bash
  docker run -d \
      --name apache2_server \
      --net ldap-network \
      -p 80:80 \
      -e LDAP_BIND_ON='cn=admin,dc=daw,dc=ieselcaminas' \
      -e LDAP_PASSWORD='ieselcaminas' \
      -e LDAP_URL='ldap://ldap-service/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub' \
      my-apache2
  ```

- **Prueba nuevamente el acceso** a `http://<IP_DEL_SERVIDOR>/test`.

### Cheatsheet: Autenticación Nginx contra LDAP

#### 1. **Preparación del Entorno**
- **Servidor LDAP**: Asegúrate de que el servidor LDAP configurado en la "Práctica 2" esté en ejecución. Obtén su IP pública.
- **Servidor Nginx**: Crea una nueva instancia EC2 en AWS o utiliza una existente con Nginx instalado.

#### 2. **Creación del Sitio Virtual en Nginx**
- Crea el directorio para el sitio virtual:
  ```bash
  sudo mkdir -p /var/www/sitioldap
  ```
- Crea el archivo `index.html`:
  ```bash
  sudo nano /var/www/sitioldap/index.html
  ```
  Contenido de `index.html`:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>Sitio LDAP!</title>
  </head>
  <body>
      <h1>Bienvenido al sitio validado por LDAP!</h1>
  </body>
  </html>
  ```

- Crea el archivo de configuración del sitio virtual:
  ```bash
  sudo nano /etc/nginx/sites-available/sitioldap
  ```
  Contenido inicial (sin autenticación LDAP):
  ```nginx
  server {
      listen 80;
      server_name sitioldap;
      root /var/www/sitioldap;
      index index.html;

      location / {
          try_files $uri $uri/ =404;
      }
  }
  ```

- Activa el sitio virtual:
  ```bash
  sudo ln -s /etc/nginx/sites-available/sitioldap /etc/nginx/sites-enabled/
  ```

- Verifica la configuración de Nginx:
  ```bash
  sudo nginx -t
  ```

- Reinicia Nginx:
  ```bash
  sudo systemctl restart nginx
  ```

- **Acceso al sitio**: Modifica el archivo `/etc/hosts` en tu máquina local para incluir la IP del servidor Nginx y el nombre `sitioldap`. Accede a `http://sitioldap` para verificar que el sitio funciona sin autenticación.

#### 3. **Instalación del Módulo de Autenticación LDAP en Nginx**
- Descarga e instala el módulo `ngx_http_auth_ldap_module`:
  ```bash
  wget https://example.com/path/to/libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb
  sudo dpkg -i libnginx-mod-http-auth-ldap_1.0.0-1_amd64.deb
  ```

- Verifica que el módulo esté habilitado:
  ```bash
  ls -la /etc/nginx/modules-enabled/
  ```

#### 4. **Configuración de la Autenticación LDAP en Nginx**
- Edita el archivo de configuración principal de Nginx (`/etc/nginx/nginx.conf`) para añadir el bloque `ldap_server`:
  ```nginx
  http {
      ldap_server mi_servidor_ldap {
          url ldap://<IP_SERVIDOR_LDAP>/ou=usuarios,dc=daw,dc=ieselcaminas?uid?sub;
          binddn "cn=admin,dc=daw,dc=ieselcaminas";
          binddn_passwd "ieselcaminas";
          group_attribute member;
          group_attribute_is_dn on;
          require valid_user;
      }

      include /etc/nginx/sites-enabled/*;
  }
  ```
  Reemplaza `<IP_SERVIDOR_LDAP>` con la IP pública del servidor LDAP.

- Modifica el archivo de configuración del sitio virtual (`/etc/nginx/sites-available/sitioldap`) para habilitar la autenticación LDAP:
  ```nginx
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

- Verifica la configuración de Nginx:
  ```bash
  sudo nginx -t
  ```

- Reinicia Nginx:
  ```bash
  sudo systemctl restart nginx
  ```

#### 5. **Prueba de Autenticación LDAP**
- Accede a `http://sitioldap` desde un navegador.
- Debería aparecer un cuadro de diálogo solicitando usuario y contraseña.
- Usa las credenciales de un usuario LDAP (por ejemplo, `profe01` y su contraseña) para autenticarte.
- Verifica que el acceso es correcto y que se muestra la página `index.html`.
