---
title: "CH4 - BSO"
---

# Cheatshet 4 - Servicios de red DNS y LDAP

## Servicio DNS (Domain Name System)

### Herramientas útiles para gestionar DNS

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