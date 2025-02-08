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

