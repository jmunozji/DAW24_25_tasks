---
title: 'CH4 - ACV'
---
DNS: 
DNS traduce nombres de dominio a direcciones IP y viceversa.

Sistemas de nombre planos:
En los albores de las redes de ARPANET, los nombres los equipos se centralizaban en un archivo llamado host.txt o /etc/hosts en Linux, que incluia el nombre del equipo y su IP. A esto es lo que se conoce como un sistema de nombres plano.

Elementos de DNS:
- Dominios: Etiquetas separadas por puntos. 
    Ejemplo: www.google.es
        - www -> Dominio de tercer nivel.
        - google -> Dominio de segundo nivel.
        - es -> Dominio de primer nivel.
- Zonas: 
    - Son gestionadas por servidores, los cuales tienen información completa sobre ellas.
    - Son almacenadas en archivos de texto o bases de datos.

Tipos de servidores DNS:
- Maestro o primario: Gestiona archivos de zona.
- Esclavo o secundario: Extrae los datos del maestro mediante la transferencia de zona.
- Caché: Sirven para mejorar los tiempos de respuesta de las consultas, reducir la carga de los equipos y disminuir el tráfico de red.
- Forwarder: Son utilizados para minimizar las consultas y el tráfico de peticiones DNS desde una red hacia Internet.
- Solo autorizado: Es un servidor primario y/o secundario que responde únicamente sobre sus zonas.
- Raíz: Autorizado para el dominio raíz. Existen 13 servidores raíz en el mundo.

Tipos de consulta:
- Recursiva: Da una respuesta completa.
- Iterativas: Proporciona una respuesta parcial.

Resolución Inversa:
Obtienes información de un nombre de dominio preguntando por la dirección IP en vez de preguntar por el nombre de domino.

Herramientas de DNS:
- Nslookup: Diagnosticos en los servidores.
- Dig: Consultas avanzadas en los servidores.
- Host: Traduce nombres de servidores y diagnósticos.
- Whois: Informa sobre los registros de dominios.

LDAP:
Protocolo que ofrece el acceso a un servicio de directorio implementado sobre un entorno de red, con el objeto de acceder a una determinada información.

OpenLDAP:
Es una implementación libre de LDAP.

¿Como funcionan?:
Se basa en entradas(un conjunto de atributos identificados por un nombre global único). Estas,  se utilizan para identificarla de forma específica.

Integración del servicio de directorio con otros servicios:
Se usan como repositorio en el cual almacenar la información que varios servidores deben compartir, por ejemplo: la configuración, información sobre el control de acceso, etc.

Formato LDIF:
Es el estándar para representar entradas del directorio en formato texto ASCII.
Este sistema destaca por su capacidad para centralizar la gestión de datos en redes complejas, mejorando la eficiencia, seguridad y consistencia de la información.