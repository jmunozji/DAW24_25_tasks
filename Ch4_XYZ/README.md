UD4: Servicio DNS (Domain Name System)
Ejemplo de fichero de nombres plano:

ARCHIVOS HOSTS

127.0.0.1   localhost
192.168.1.100   servidor-local
8.8.8.8   google-dns

ARCHIVOS NETWORKS

loopback    127
link-local  169.254
default     0.0.0.0

ARCHIVO DE CONFIGURACIÓN DE DNS EN resolv.conf

nameserver 8.8.8.8
nameserver 8.8.4.4
domain example.com
search local.example.com

ARCHIVO LMHOSTS

192.168.1.1   SERVIDOR #Comentario opcional
192.168.1.2   CLIENTE

ARCHIVO ETHERS

08:00:27:ea:db:2a   servidor-web
00:1A:2B:3C:4D:5E   cliente-local

DOMINIO GENÉRICO (TLD - TOP-LEVEL DOMAIN)
.com
.org
.net
.edu

DOMINIO DE NIVEL INFERIOR (SECOND-LEVEL DOMAIN)
microfoft.com
bbc.co.uk

SUBDOMINIOS
mail.google.com
shop.amazon.com
blog,example.org

DOMINIOS DE PAÍS (ccTLD - Country Code Top-Level Domain)
.es
.fr
.mx
.us

DOMINIOS INTERNACIONALIZADOS (IDN - Internationalized Domain Names)
español.es

NOMBRES COMPLETOS Y RESOLUCIONES
www.example.com.
192.0.2.1 = server.example.com.

APLICACIONES PRÁCTICAS
Correos electrónicos:

usuario@dominio.com
dominio.com

Servicios específicos:

mx1.dominio.com
cdn.dominio.com

TIPOS DE RR
Registros Básicos

A (Addres Record)
www.example.com. IN A 192.0.2.1
AAAA (IPv6 Addres Record)
www.example.com. IN AAAA 2001:0db8::1
CNAME (Canonical Name Record)
blog.example.com. IN CNAME www.example.com.
MX (Mail Exchange Record)
example.com. IN MX 10 mail.example.com.
NS (Name Server Record)
example.com. IN NS ns1.example.com.
PTR (Pointer Record)
1.2.0.192.in-addr.arpa. IN PTR www.example.com.
SOA (Start of Authority Record)
example.com. IN SOA ns1.example.com. admin.example.com. (
    2023120101 ; Serial
    3600       ; Refresh
    1800       ; Retry
    1209600    ; Expire
    86400      ; Minimum TTL
)

TIPOS DE SERVIDORES NDS

SERVIDOR DNS MAESTRO
Características:
1. Almacenamiento de la zona original
2. Responsable de la actualización de resgistros
3. Sincronización con los servidores esclavos (secundarios)
4. No tiene caché
   
FUNCIONAMIENTO
1. Gestión de registros
2. Transferencia de zona
3. Respuestas Autoritativas

VENTAJAS DEL SERVIDOR MAESTRO
Control total sobre la zona
Evita inconsistencias

DESVENTAJAS
Punto único de fallo
Dependencia de la infraestructura

EJEMPLO DE CONFIGURACIÓN

$TTL 86400
@    IN    SOA   ns1.example.com. admin.example.com. (
             2023120101 ; Serial
             3600       ; Refresh
             1800       ; Retry
             1209600    ; Expire
             86400 )    ; Minimum TTL

     IN    NS    ns1.example.com.
     IN    NS    ns2.example.com.

ns1  IN    A     192.0.2.1
ns2  IN    A     192.0.2.2
www  IN    A     192.0.2.10
mail IN    MX    10 mail.example.com.

SERVIDOR DNS ESCLAVO
Características:
1. Copia de los registros del servidor maestro
2. Responde a consultas
3. Redundancia y alta disponibilidad
4. Sincronización automática
5. No puede modificar los registros
6. Sin cache de registros

FUNCIONAMIENTO
1. Transferencia de zona
2. Actualización automática

VENTAJAS
1. Distribución de carga
2. Recuperación ante fallos
3. Seguridad

DESVENTAJAS
1. Dependencia del SERVIDOR MAESTRO
2. No tiene control de los registros
3. Sincronización

EJEMPLO DE CONFIGURACIÓN

zone "example.com" {
    type slave;
    file "/var/named/example.com.db";
    masters { 192.0.2.1; };   # Dirección IP del servidor maestro
};

SERVIDOR DNS CACHÉ
Características:
1. Resolución recursiva
2. Almacenamiento en Caché
3. Mejora de velocidad
4. Eficiencia y reducción de carga
5. Tiempo de vida (TTL)
6. No guarda la información definitiva
7. Reducción del tráfico en la red

FUNCIONAMIENTO
1. Consulta inicial
2. Si la respuesta está en caché
     Si la respuesta está almacenada en la caché y no ha expirado (es decir, el TTL no ha vencido), el servidor DNS de caché devuelve inmediatamente la respuesta al cliente.
3. Si la respuesra no está en caché
    Si la respuesta no está en caché, el servidor recursivo realiza la búsqueda comenzando con los servidores raíz y luego hacia los servidores autoritativos, realizando una resolución completa de la consulta.
4. Almacenamiento en caché
5. Respuestas futuras

VENTAJAS
1. Reducción de la latencia
2. Descongestión de los servidores autoritativos
3. Mejora de la eficiencia
4. Reducción de costos de tráfico de red

DESVENTAJAS
1. Riesgo de información obsoleta
2. Ataques de envenamiento de caché
3. Dependencia de la caché

EJEMPLO DE CONFIGURACIÓN

options {
    directory "/var/named";
    recursion yes;  # Habilitar resolución recursiva
    allow-query { any; };  # Permitir consultas desde cualquier cliente
    forwarders {
        8.8.8.8;  # Servidor DNS de Google (si quieres reenvío)
        1.1.1.1;  # Servidor DNS de Cloudflare
    };
    dnssec-validation yes;  # Habilitar validación de DNSSEC
};

SERVIDOR DNS forwarders
Caracteristísticas:
Mejorar el rendimiento al almacenar en caché respuestas frecuentes
Delegar la resolución de nombres a un servidor externo más capacitado
Restringir el acceso directo a servidores DNS públicos, mejorando la Seguridad

VENTAJAS
1. Optimización
2. Seguridad
3. Escalabilidad

CONFIGURACIÓN DNS Forwarder (BIND e Linux)
1. Instalar BIND:

sudo apt update
sudo apt install bind9

2. Editar archivo:

sudo nano /etc/bind/named.conf.options

3. Agregar los forwarders:

options {
    directory "/var/cache/bind";

    forwarders {
        8.8.8.8; // Google DNS
        1.1.1.1; // Cloudflare DNS
    };

    forward only;

    dnssec-validation auto;

    listen-on-v6 { any; };
};

4. Reiniciar el servicio:

sudo systemctl restart bind9

SERVIDOR DNS AUTORIZADO
1. Editar la configuración d BIND:

sudo nano /etc/bind/named.conf.options

2. Agregar una ACL:

acl "authorized_clients" {
    192.168.1.0/24;  // Red local
    10.0.0.1;        // Cliente específico
};

options {
    directory "/var/cache/bind";
    allow-query { authorized_clients; };
    forwarders { 8.8.8.8; 1.1.1.1; };
};

3. Reiniciar el servicio

sudo systemctl restart bind9

SERVIDORES raíz
Como interactúan los servidores raíz:

1. Consulta inicial
2. Redirección
3. Delegación adicional

CONFIGURACIÓN
Archivos de hints:

curl -O https://www.internic.net/domain/named.root

CONFIGURA BIND PARA USARLO:

sudo nano /etc/bind/named.conf.options


ASEGURAR DE QUE SE APUNTE:

options {
    directory "/var/cache/bind";
    recursion yes;
    allow-query { any; };
    forwarders { }; // No usar forwarders
    root-hints "/etc/bind/named.root";
};


REINICIAR BIND

sudo systemctl restart bind9

RESOLUCIÓN INVERSA
COMO FUNCIONA:

1. Dirección IP en formato inverso:
Para IPv4: in-addr.arpa
Para IPv6: ip6.arpa
EJEMPLO:
 IPv4 192.168.1.10 se convierte 10.1.168.192.in-addr.arpa
 IPv6 2001:db8::1 se convierte 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.8.b.d.0.1.0.0.2.ip6.arpa
 2. Consultar en el PTR Record

 CONFIGURACIÓN
 1. Configurar el archivo de zona INVERSA

 sudo nano /etc/bind/named.conf.local

2. Crear el archivo de zona

sudo nano /etc/bind/db.192.168.1

3. Reiniciar BIND

sudo systemctl restart bind9

HERRAMIENTAS
NSLOOKUP
Comandos básicos:
1. Resolución de nombres
nslookup example.com
1.1. Salida típica

Server:  8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
Name:    example.com
Address: 93.184.216.34
2. Consulta INVERSA
nslookup 93.184.216.34
2.1 Salida típica

Server:  8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
34.216.184.93.in-addr.arpa  name = example.com.

3. Especificar un servidor DNS
nslookup example.com 1.1.1.1

MODO INTERACTIVO
nslookup

1. Consultar un dominio
> example.com
2. Consultar registros específicos
> set type=MX
> example.com
3. Cambiar el servidor DNS
> server 8.8.8.8
4. Habilitar/Deshabilitar resolución automática
> set norecurse

DIG 
1. Resolución directa
dig example.com
2. Consulta INVERSA
dig -x 93.184.216.34
3. Especificar un servidor DNS
dig @8.8.8.8 example.com

HOSTS
1. Resolver un nombre de dominio
host example.com
2. Consultar una dirección IP
host 93.184.216.34

ESPECIFICAR UN SERVIDOR DNS
host example.com 8.8.8.8

WHOIS
1. Consultar un dominio
whois example.com
2. Consultar una dirección IP
whois 8.8.8.8

CONFIGURACIÓN
Archivo de configuración del sistema

/etc/whois.conf

Especificar un servidor WHOIS manualmente

whois -h whois.verisign-grs.com example.com

Configuración para extensiones específicas

.io whois.nic.io

Cambiar el puerto predeterminado

whois -p 43 example.com
