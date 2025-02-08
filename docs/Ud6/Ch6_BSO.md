---
title: 'CH6 - BSO'
---

# **T6: Servidores de aplicaciones**

## Apache Tomcat para desplegar Aplicaciones Java
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

1. `sudo apt-get install maven`: Instalación de maven
1. `mvn --v`: Comprobar si está correctamente instalada
1. Indicar a Maven el servidor que vamos a desplegar(Tomcat)
    1. `/etc/tomcat10/tomcat-users.xml`
        ```xml
        <role rolename="admin-gui"/>
        <role rolename="manager-gui"/>
        <role rolename="manager-script"/>
        <user username="admin" password="ieselcaminas" roles="admin-gui,manager-gui"/>
        <user username="despliegues" password="ieselcaminas" roles="manager-script"/>
        ```
    1. `sudo systemctl restart tomcat10.service`: Reiniciar tomcat
    1. `/etc/maven/settings.xml`
        ```xml
        <server>
            <id>DesplieguesTomcat</id>
            <username>despliegues</username>
            <password>ieselcaminas</password>
        </server>
        ```
1. `pom.xml`: Este archivo está en el proyecto que queremos desplegar
    ```diff
    <build>
        <finalName>roshambo</finalName> #
    +    <plugins> 
    +        <plugin>
    +            <groupId>org.apache.tomcat.maven</groupId>
    +            <artifactId>tomcat7-maven-plugin</artifactId>
    +            <version>2.2</version>
    +            <configuration>
    +                <url>http://localhost:8080/manager/text</url> #
    +                <server>DesplieguesTomcat</server> #
    +                <path>/rps</path> #
    +            </configuration>
    +        </plugin>
        </plugins>
    </build>
    ```
1. `mvn tomcat7:deploy`: Desplegar proyecto

## Node.js y Express para desplegar aplicaciones JavasCript

![Imagen Node con express](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Finternet-israel.com%2Fwp-content%2Fuploads%2F2016%2F04%2Fexpress.jpg&f=1&nofb=1&ipt=43d5483fbf6c897147a4c97f3ec181f7f5d353d98d50aed9450941728447a179&ipo=images)

### Instalación de Node.js

```bash
#Añadir repositorio de Node en Debian
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

#Indicamos que versión de Node instalar
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sour
ces.list.d/nodesource.list

#Instalación de Node
sudo apt-get update
sudo apt-get install nodejs -y
```

### Instalación de Express

```sh
sudo npm install -g express #Instalación de express de manera global
node app.js #Ejecutar Aplicación con Node
```

### Despliegue de un proyecto con Express

1. Consigues el proyecto que quieres desplegar, ya sea mediante git o uno que ya tengas en tu PC.
1. `cd proyecto`: Entras a la carpeta del proyecto.
1. `npm install`: Instalas las dependencias necesarias.
1. `package.json`: Consultas cómo desplegar aplicación.
    ```json
    "scripts": {
    "start:watch": "nodemon ./bin/www --ignore public/",
    "start:dev": "node ./bin/www",
    "debug": "node debug ./bin/www",
    "start": "NODE_ENV=production node ./bin/www",
    },
    ```
1. `npm run start`: Inicias la aplicación.
1. Acceder a la ruta que te da, `http://IP_PUBLICA:3000`

### Clusterizar una aplicación mediante Node

```bash 
npm install cluster #Instalamos cluster
```

