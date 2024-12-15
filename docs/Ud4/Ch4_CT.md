# Cheat Sheet: Servicios de Red - DNS y LDAP

---

## DNS (Domain Name System)
### Conceptos Clave
- **Función**: Traduce nombres de dominio a direcciones IP y viceversa.
- **Jerarquía**: Estructura en forma de árbol invertido.
- **Zonas**: Porciones del espacio de nombres administradas por un servidor autorizado.

### Tipos de Servidores DNS
- **Maestro (Primario)**: Gestiona archivos de zona editables.
- **Esclavo (Secundario)**: Copia de solo lectura del maestro.
- **Cache**: Almacena respuestas para mejorar tiempos de consulta.
- **Forwarder**: Reenvía consultas a otros servidores.
- **Solo autorizado**: Responde solo sobre sus zonas, no realiza recursividad.
- **Servidores raíz**: Autorizados para el dominio raíz (`.`).

### Tipos de Consultas
- **Recursiva**: El servidor entrega la respuesta completa.
- **Iterativa**: El servidor da referencias para seguir la consulta.

### Registros de Recursos (RR)
| **Tipo**  | **Descripción**                                                                 |
|-----------|---------------------------------------------------------------------------------|
| `SOA`     | Información de la zona (servidor principal, email del admin, etc.).            |
| `NS`      | Servidores autorizados para una zona.                                          |
| `A`       | Asocia un nombre de dominio con una dirección IPv4.                            |
| `CNAME`   | Define alias para un dominio.                                                  |
| `MX`      | Especifica servidores de correo.                                               |
| `PTR`     | Resolución inversa (IP -> dominio).                                            |
| `TXT`     | Información adicional (e.g., claves de seguridad).                             |

### Herramientas Útiles
- **`nslookup`**: Diagnóstico DNS.
- **`dig`**: Consultas avanzadas y flexibles.
- **`host`**: Consultas simples.
- **`whois`**: Información de registro de dominios.

---

## LDAP (Lightweight Directory Access Protocol)
### Conceptos Clave
- **Función**: Acceso a un servicio de directorio para organizar datos jerárquicos.
- **DIT (Directory Information Tree)**: Estructura jerárquica de entradas.
- **DN (Distinguished Name)**: Identificador único de una entrada.

### Atributos Comunes
| **Atributo** | **Descripción**                         |
|--------------|-----------------------------------------|
| `uid`        | Identificación única del usuario.       |
| `cn`         | Nombre común.                          |
| `sn`         | Apellido.                              |
| `mail`       | Correo electrónico.                    |
| `objectClass`| Tipo de objeto (persona, grupo, etc.).  |

### Formato LDIF
- Representación estándar en texto:
```ldif
dn: uid=usuario, ou=People, dc=ejemplo, dc=com
objectclass: account
cn: Usuario Ejemplo
mail: usuario@ejemplo.com
