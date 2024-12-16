# CHEATSHEEET TEMA 4 DNS ALEXANDRU EDUARD MUSAT

## **1. Comandos para Configuración de DNS**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Instalar BIND9                      | `sudo apt update`<br>`sudo apt install bind9`             |
| Editar archivo de configuración     | `sudo nano /etc/bind/named.conf.local`                    |
| Editar archivos de zona             | `sudo nano /etc/bind/db.ejemplo.com`<br>`sudo nano /etc/bind/db.192.168.1` |
| Verificar configuración             | `sudo named-checkconf`<br>`sudo named-checkzone ejemplo.com /etc/bind/db.ejemplo.com` |
| Reiniciar el servicio DNS           | `sudo systemctl restart bind9`                            |
| Consultar el servidor DNS           | `nslookup ejemplo.com`<br>`dig @localhost ejemplo.com`     |

---

## **2. Comandos para Dockerizar DNS**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Construir imagen Docker             | `docker build -t dns_bind9 .`                              |
| Ejecutar contenedor                 | `docker run -d --name servidor_dns -p 53:53 -p 53:53/udp dns_bind9` |
| Ver logs del contenedor             | `docker logs servidor_dns`                                |
| Acceder al contenedor               | `docker exec -it servidor_dns /bin/bash`                  |
| Listar contenedores activos         | `docker ps`                                               |

---

## **3. Comandos para LDAP**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Instalar OpenLDAP                   | `sudo apt update`<br>`sudo apt install slapd ldap-utils`  |
| Configurar OpenLDAP                 | `sudo dpkg-reconfigure slapd`                             |
| Verificar el servicio LDAP          | `sudo systemctl status slapd`                             |
| Buscar entradas LDAP                | `ldapsearch -x -LLL -H ldap:/// -b dc=ejemplo,dc=com`      |
| Añadir entradas con LDIF            | `sudo ldapadd -x -D "cn=admin,dc=ejemplo,dc=com" -W -f archivo.ldif` |
| Modificar entradas con LDIF         | `sudo ldapmodify -x -D "cn=admin,dc=ejemplo,dc=com" -W -f cambios.ldif` |
| Eliminar entradas LDAP              | `ldapdelete -x -D "cn=admin,dc=ejemplo,dc=com" -W "uid=usuario,dc=ejemplo,dc=com"` |

---

## **4. Comandos para Dockerizar LDAP**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Descargar imagen de OpenLDAP        | `docker pull osixia/openldap`                             |
| Crear contenedor LDAP               | `docker run --name ldap_container -p 389:389 -p 636:636 -e LDAP_DOMAIN="ejemplo.com" -e LDAP_ADMIN_PASSWORD="admin" -d osixia/openldap` |
| Verificar el contenedor LDAP        | `docker logs ldap_container`                              |
| Consultar el servidor LDAP          | `ldapsearch -x -H ldap://localhost -b dc=ejemplo,dc=com`   |

---

## **5. Comandos para Configurar Apache con LDAP**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Instalar módulos LDAP para Apache   | `sudo apt install apache2 libapache2-mod-ldap-userdir`    |
| Habilitar módulo de autenticación   | `sudo a2enmod authnz_ldap`                                |
| Reiniciar Apache                    | `sudo systemctl restart apache2`                          |

---

## **6. Comandos para Configurar Nginx con LDAP**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Instalar nginx-extras               | `sudo apt install nginx-extras`                           |
| Editar archivo de configuración     | `sudo nano /etc/nginx/sites-available/default`            |
| Reiniciar servicio Nginx            | `sudo systemctl restart nginx`                            |

---

## **7. Comandos Generales de Docker**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Listar imágenes                     | `docker images`                                           |
| Listar contenedores                 | `docker ps`                                               |
| Listar todos los contenedores       | `docker ps -a`                                            |
| Eliminar un contenedor              | `docker rm <container_id>`                                |
| Eliminar una imagen                 | `docker rmi <image_id>`                                   |

---

## **8. Comandos de Debugging**
| **Acción**                          | **Comando**                                                |
|-------------------------------------|-----------------------------------------------------------|
| Ver logs del sistema                | `sudo tail -f /var/log/syslog`                            |
| Inspeccionar registros LDAP         | `sudo slapcat`                                            |
| Ver logs de un contenedor Docker    | `docker logs <container_id>`                              |

---

### **Configuraciones Importantes**
| **Archivo**                     | **Configuración**                                                                                                         |
|----------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| `/etc/bind/named.conf.local`    | Configuración de zonas DNS                                                                                                |
| `/etc/bind/db.ejemplo.com`      | Zona directa: definición de registros A, NS, etc.                                                                          |
| `/etc/bind/db.192.168.1`        | Zona inversa: definición de registros PTR                                                                                 |
| `base.ldif`                     | Creación de base LDAP                                                                                                     |
| `usuarios.ldif`                 | Creación de usuarios LDAP                                                                                                 |
| `/etc/apache2/sites-available`  | Configuración de autenticación LDAP en Apache                                                                              |
| `/etc/nginx/sites-available`    | Configuración de autenticación LDAP en Nginx                                                                               |

---

## Archivos de configuración


## **1. Configuración de DNS**
### **Archivos de configuración**
#### Archivo `/etc/bind/named.conf.local`:
```bash
zone "ejemplo.com" {
    type master;
    file "/etc/bind/db.ejemplo.com";
};

zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.1";
};
```

#### Archivo `/etc/bind/db.ejemplo.com`:
```bash
$TTL    604800
@       IN      SOA     ns1.ejemplo.com. admin.ejemplo.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.ejemplo.com.
ns1     IN      A       192.168.1.1
www     IN      A       192.168.1.10
```

#### Archivo `/etc/bind/db.192.168.1`:
```bash
$TTL    604800
@       IN      SOA     ns1.ejemplo.com. admin.ejemplo.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.ejemplo.com.
1       IN      PTR     ns1.ejemplo.com.
10      IN      PTR     www.ejemplo.com.
```

---

## **2. Configuración de LDAP**
### **Archivos de configuración**
#### Ejemplo de archivo LDIF para añadir una base organizativa:
Archivo `base.ldif`:
```bash
dn: dc=ejemplo,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: ejemplo
dc: ejemplo
```

#### Archivo LDIF para añadir usuarios:
Archivo `usuarios.ldif`:
```bash
dn: uid=usuario1,ou=People,dc=ejemplo,dc=com
objectClass: inetOrgPerson
cn: Usuario Uno
sn: Uno
uid: usuario1
userPassword: password123
mail: usuario1@ejemplo.com
```

#### Configuración de `/etc/ldap/ldap.conf`:
```bash
BASE    dc=ejemplo,dc=com
URI     ldap://localhost
```

#### Archivo de esquema para slapd (opcional):
```bash
include         /etc/ldap/schema/core.schema
include         /etc/ldap/schema/cosine.schema
include         /etc/ldap/schema/inetorgperson.schema
```

---

## **3. Dockerización de DNS y LDAP**
### **Dockerfile para BIND9**:
Archivo `Dockerfile`:
```bash
FROM ubuntu:latest

RUN apt-get update && apt-get install -y bind9

COPY named.conf.local /etc/bind/named.conf.local
COPY db.ejemplo.com /etc/bind/db.ejemplo.com
COPY db.192.168.1 /etc/bind/db.192.168.1

CMD ["named", "-g"]
```

### **Dockerfile para LDAP**:
Archivo `Dockerfile`:
```bash
FROM osixia/openldap:latest

ENV LDAP_DOMAIN="ejemplo.com"
ENV LDAP_ADMIN_PASSWORD="admin123"
COPY base.ldif /container/service/slapd/assets/config/bootstrap/ldif/50-base.ldif
COPY usuarios.ldif /container/service/slapd/assets/config/bootstrap/ldif/51-usuarios.ldif
```

---

## **4. Configuración de Apache con LDAP**
### **Configuración en `/etc/apache2/sites-available/000-default.conf`**:
```bash
<VirtualHost *:80>
    ServerName ejemplo.com

    <Location />
        AuthType Basic
        AuthName "LDAP Authentication"
        AuthBasicProvider ldap
        AuthLDAPURL "ldap://localhost:389/ou=People,dc=ejemplo,dc=com?uid"
        AuthLDAPBindDN "cn=admin,dc=ejemplo,dc=com"
        AuthLDAPBindPassword admin123
        Require valid-user
    </Location>
</VirtualHost>
```

### **Activar módulos y reiniciar Apache**:
```bash
sudo a2enmod authnz_ldap
sudo systemctl restart apache2
```

---

## **5. Configuración de Nginx con LDAP**
### **Instalación del módulo `nginx-extras`**:
```bash
sudo apt install nginx-extras
```

### **Configuración en `/etc/nginx/sites-available/default`**:
```bash
server {
    listen 80;
    server_name ejemplo.com;

    location / {
        auth_ldap "Restricted";
        auth_ldap_servers ldap_server;

        root /var/www/html;
        index index.html index.htm;
    }
}

ldap_server ldap_server {
    url ldap://localhost:389/ou=People,dc=ejemplo,dc=com?uid?sub?(objectClass=inetOrgPerson);
    binddn "cn=admin,dc=ejemplo,dc=com";
    binddn_passwd admin123;
    group_attribute memberUid;
    group_attribute_is_dn off;
}
```

---

## **6. Docker Compose para LDAP y Nginx**
Archivo `docker-compose.yml`:
```yaml
version: '3.7'
services:
  ldap:
    image: osixia/openldap
    environment:
      LDAP_DOMAIN: "ejemplo.com"
      LDAP_ADMIN_PASSWORD: "admin123"
    ports:
      - "389:389"

  nginx:
    image: nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "80:80"
```