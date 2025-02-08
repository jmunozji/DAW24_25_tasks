---
title: 'CH6 - BSO'
---

# **T6: Servidores de aplicaciones**

## APACHE TOMCAT
![Imagen Apache Tomcat](https://itdconsulting.com/wp-content/uploads/2022/04/banner-pestana-apache-tomcat.jpg)

### Instalación de Tomcat

`sudo apt-get update && apt-get upgrade`:  Actualizar antes
`java --version`: Comprobar si java está instalado

Si no está instalado java, lo instalaremos usando:

```bash
sudo apt-get install default-jre
sudo apt-get install default-jre
```

Instalar Tomcat

```bash
sudo apt-get install -y tomcat10 tomcat10-admin #Instalación de Tomcat
sudo systemctl status tomcat10 #Comprobamos si está instalado
```

Tips para habilitar tomcat o cualquier servicio

```bash
sudo systemctl start tomcat10 #Iniciamos tomcat
sudo systemctl enable tomcat10 #Conseguimos que el servicio se inicie
                               #siempre al encender el PC
```

Comprobar acceso a tomcat desde un navegador

`http://IP_SERVIDOR:8080`: Acceder desde una navegador

![Imagen tomcat navegador](https://jmunozji.github.io/DAW/Ud6%20Servidores%20de%20aplicaciones/P3_1/03.png)

### Configuración de Tomcat

#### Gestión de Usuarios y roles

```bash
sudo nano /etc/tomcat10/tomcat-users.xml #Modificamos este fichero
```

- "Gestor de Aplicaciones Web de Tomcat" deberemos activar el rol "manager-gui".
- "Gestor de Máquina Virtual de Tomcat" necesitaremos activar el rol "admin-gui".

```xml
  <!--Añadimos estas líneas antes del cierre-->
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
```

Si reiniciamos tomcat con `sudo systemctl restart tomcat10`, e ingresamos a `http://IP_SERVIDOR:8080/manager`, podemos acceder al manager, donde nos prodremos logear.

![Imagen del manager de Tomcat](https://jmunozji.github.io/DAW/Ud6%20Servidores%20de%20aplicaciones/P3_1/04.png)

### Despliegue de Aplicaciones Web de Tomcat

#### Mediante un archivo .war

1. Descargamos el archivo .war
1. Dentro del manager, buscamos donde poder seleccionar el archivo y desplegar el .war
1. Acceder a la aplicación mediante `http://IP_SERVIDOR:8080/aplicacion`

#### Mediante Maven

- `sudo apt-get install maven`: Instalación de maven
- 



