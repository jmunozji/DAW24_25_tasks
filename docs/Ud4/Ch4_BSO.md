---
title: "CH4 - BSO"
---

# Cheatshet 4 - Servicios de red DNS y LDAP

## Servicio DNS (Domain Name System)

### Herramientas útiles para gestionar DNS

```bash
sudo apt-get install dnsutils  #Instalar estas herramientas en Debian
```

- `nslookup`: Se utiliza para saber si un servidor DNS resuelve correctamente los nombres DNS y las direcciones IP.

    - nslookup cisco.com

```bash
Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
Name:	cisco.com
Address: 72.163.4.185
Name:	cisco.com
Address: 2001:420:1101:1::185
```

- `dig`: Utilizada para solucionar problemas de DNS gracias a su facilidad de uso y claridad en la presentación de la información.

    - dig cisco.com

```bash
; <<>> DiG 9.20.3 <<>> cisco.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28149
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;cisco.com.			IN	A

;; ANSWER SECTION:
cisco.com.		1621	IN	A	72.163.4.185

;; Query time: 13 msec
;; SERVER: 1.1.1.1#53(1.1.1.1) (UDP)
;; WHEN: Sat Dec 14 16:40:58 CET 2024
;; MSG SIZE  rcvd: 54
```

- `host`: Herramienta CLI sencilla y fácil de usar para realizar consultas DNS.

    - host cisco.com

```bash
cisco.com has address 72.163.4.185
cisco.com has IPv6 address 2001:420:1101:1::185
cisco.com mail is handled by 10 alln-mx-01.cisco.com.
cisco.com mail is handled by 20 rcdn-mx-01.cisco.com.
cisco.com mail is handled by 30 aer-mx-01.cisco.com.
```

- `whois`: Aunque no es una herramienta de diagnóstico DNS si que nos ofrece información sobre el registro del dominio.

    - whois cisco.com

```bash
Domain Name: CISCO.COM
   Registry Domain ID: 4987030_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.markmonitor.com
   Registrar URL: http://www.markmonitor.com
   Updated Date: 2024-04-13T09:39:41Z
   Creation Date: 1987-05-14T04:00:00Z
   Registry Expiry Date: 2025-05-15T04:00:00Z
   Registrar: MarkMonitor Inc.
   Registrar IANA ID: 292
   Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
   Registrar Abuse Contact Phone: +1.2086851750
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited
   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited
   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited
   Name Server: NS1.CISCO.COM
   Name Server: NS2.CISCO.COM
   Name Server: NS3.CISCO.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2024-12-14T15:42:34Z <<<
```

### Instalación de un servidor DNS

```bash
sudo apt-get install bind9 bind9utils bind9-doc 
```

`sudo nano /etc/systemd/resolved.conf`: cambiar el DNS al que consulta nuestro servidor.

```resolved.conf
[Resolve]
DNS=127.0.0.1
```

`sudo systemctl restart systemd-resolved`: Aplica la configuración

### Configuración de un servidor DNS

- `/etc/bind/named.conf`: Archivo de configuración principal

    - Sirve simplemente para aglutinar o agrupar a los archivos de configuración que usaremos.

```named.conf
// This is the primary configuration file for the BIND DNS server named.
//
// Please read /usr/share/doc/bind9/README.Debian for information on the
// structure of BIND configuration files in Debian, *BEFORE* you customize
// this configuration file.
//
// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";
```

- `named.conf.options`: Se encarga de contener las configuraciones globales.

- `named.conf.local`: En este archivo configuraremos aspectos relativos a nuestras zonas.

- `named.conf.default-zones`: Se encarga de definir las zonas predeterminadas.

### Creación de un archivo de zona

`sudo nano named.conf.local`

```named.conf.local
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "deaw.es" {
        type master;
        file "/etc/bind/db.deaw.es";  //Ruta donde ubicamos nuestro archivo de zona
};
```

`sudo nano /etc/bind/db.deaw.es`

```/etc/bind/db.deaw.es
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

- `$TTL`: Tiempo de vida predeterminado para los registros de la zona.

- `SOA (Start of Authority)`: Indica información sobre la zona, como el nombre del servidor primario, el contacto del administrador y detalles de tiempo.

- `NS`: Registra los servidores de nombres autoritativos para la zona.

- `A`: Registra la dirección IP asociada con un nombre de host específico.

#### Comprobaciones importantes de la configuración

- `named-checkconf`: Se utiliza para comprobar que no hay errores en la configuración.

- `named-checkzone deaw.es /etc/bind/db.deaw.es`: Comprueba si la zona configurada es correcta.

```bash
$ sudo named-checkzone deaw.es /etc/bind/db.deaw.es
zone deaw.es/IN: loaded serial 2023112301
OK
```

- `sudo systemctl restart systemd-resolved`: Si todo va correctamente, se reinicia el servicio.

### Zona de resolución inversa

```conf
# /etc/bind/named.conf.local
zone "104.85.3.in-addr.arpa" {
    type master;
    file "/etc/bind/db.3.85.104";  # Ruta al archivo de zona inversa
};
```

```conf
# /etc/bind/db.3.85.104
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

## Servicio de directorio LDAP (Lightweight Directory Access Protocol)

### Instalación del servidor LDAP

- `sudo apt install slapd ldap-utils`: Instala el servidor LDAP

- `sudo dpkg-reconfigure slapd`: Te permite configurar desde cero el servidor LDAP o reconfigurarlo si ya lo tenías.

### Administración del servidor LDAP

- `sudo slapcat`: Te permite ver la información actual que tienes configurada en LDAP.

- `sudo apt-get install phpldapadmin`: Instala phpldapadmin, con lo que podemos configurar nuestro servidor ldap desde una interfaz gráfica desde un navegador.

```diff
#/etc/phpldapadmin/config.php --> Cambiamos el DIT (Directory Information Tree)
-   $servers->;setValue('server','base',array('dc=example,dc=com'));
-   $servers->setValue('login','bind_id','cn=admin,dc=example,dc=com');

+   $servers->setValue('server','base',array('dc=daw,dc=ieselcaminas'));
+   $servers->setValue('login','bind_id','cn=admin,dc=daw,dc=ieselcaminas');
```

### Configuración del servidor LDAP

