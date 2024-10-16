---
title: 'CH1 - Roberto Percuto Escrig'
---

# Cheatsheet de comandos Git

## Instalación

Para instalar git tendremos que tener el sistema y los directorios actualizados. Para ello utilizaremos el comando:

```bash
sudo apt-get update && sudo apt-get upgrade
```

Una vez actualizado, para instalar git ejecutaremos el siguiente comando:

```bash
apt-get install git
```

---

## Configuración

### Identidad

Si queremos configurar un nombre de usuario, utilizaremos la siguiente orden:

```bash
git config --global user.name "[nombre del usuario]"
```

Si lo que queremos es establecer el email del usuario, usaremos:

```bash
git config --global user.email [email del usuario]
```

### Recomendaciones

Se recomienda configurar el push por defecto a simple con el comando:

```bash
git config --global push.default simple
```

---

## Crear y gestionar un proyecto

El comando para crear un repositorio en el directorio actual es:

```bash
git init
```

Para añadir el archivo deseado al repositorio o agregar los cambios del mismo al área de preparación, usaremos el comando:

```bash
git add [nombre del archivo]
```

Para comprobar el estado de los archivos en el repositorio utilizaremos:

```bash
git status
```

El comando utilizado para guardar los cambios en el repositorio es (se recomienda poner la opción -m ya que así puedes especificar el mensaje directamente en la orden):

```bash
git commit -m "[mensaje]"
```

---

## Alias

Para crear un alias, usaremos:

```bash
git config --global alias.[nombre del alias] "ordenes del alias"
```

---

## Utilizar el historial

Para poder observar los cambios que hemos hecho, más específicamente los commits, ejecutaremos el comando (para salir, pulsar `q`):

```bash
git log
```

Para verlo de una forma más abreviada, usaremos:

```bash
git log --oneline
```

Si lo que queremos es regresar a alguna versión anterior, utilizaremos el comando:

```bash
git checkout [commit id]
```

---

## Etiquetas

Para etiquetar versiones del repositorio, ejecutaremos la orden:

```bash
git tag [nombre etiqueta]
```

Si queremos borrar una etiqueta creada, utilizaremos:

```bash
git tag -d [nombre_etiqueta]
```

El comando utilizado para ver los cambios entre dos commits es:

```bash
git diff [commit1] [commit2]
```

---

## Deshacer cambios

Si queremos deshacer los cambios que hemos añadido al Staging Area, usaremos el comando:

```bash
git restore --staged [archivo]
```

Para volver a la última versión del archivo, utilizaremos:

```bash
git restore [archivo]
```

En el caso que queramos deshacer un commit, ejecutaremos:

```bash
git revert HEAD --no-edit
```

El comando utilizado para deshacer un commit sin dejar rastro de ello será:

```bash
git reset --hard [commit1]
```

Si lo que queremos es modificar el último commit, el comando a utilizar es:

```bash
git commit --amend -m [Mensaje nuevo]
```

---

## Mover archivos

Para mover archivos, usaremos el comando:

```bash
git mv [archivo] [nueva localización]
```

---

## Eliminar archivos

Si queremos eliminar un archivo de git, es:

```bash
git rm [archivo]
```

---

## Ramas

El comando utilizado para listar todas las ramas es:

```bash
git branch
```

Para crear una rama sin entrar en ella, utilizaremos:

```bash
git branch [nombre rama]
```

Una vez creada, para cambiar a esa rama ejecutaremos:

```bash
git checkout [nombre rama]
```

Si queremos hacer las dos a la vez, usaremos:

```bash
git checkout -b [nombre rama]
```

---

### Fusión de ramas

El comando utilizado para fusionar ramas es (se fusionarán la rama seleccionada con la actual):

```bash
git merge [nombre rama con la que nos queremos fusionar]
```

Si queremos que quede un registro de la fusión, tendremos que utilizar:

```bash
git merge -m "[mensaje]" --no-ff [nombre de la rama]
```

Si no queremos mantener el historial de ambas ramas, utilizaremos el comando:

```bash
git rebase [nombre rama con la que nos queremos fusionar]
```

---

## Configuración SSH

Para crear una clave ssh, utilizaremos el comando:

```bash
ssh-keygen -t rsa -C "[nombre]"
```

Para vincular a un repositorio de GitHub ya existente, usaremos:

```bash
git remote add origin git@github.com:[usuario]/[nombre-repositorio.git]
```

Si queremos cambiar el nombre de la rama principal, utilizaremos:

```bash
git branch -M [nombre]
```

---

## Subir el proyecto a GitHub

El siguiente comando envía los cambios locales al repositorio de GitHub (la opción -u establece una relación entre la rama main local y remota):

```bash
git push -u [repositorio] [rama destino]
```

---

## Clonar un repositorio de GitHub

Para clonar un repositorio, usaremos el comando:

```bash
git clone git@github.com:[usuario]/[nombre-repositorio.git]
```

Si queremos ver la configuración del repositorio remoto, utilizaremos:

```bash
git remote show [nombre del remoto]
```

Usaremos este comando para traer la información de los cambios, pero no los cambios del servidor en sí:

```bash
git fetch
```

Para traer los cambios, necesitaremos el comando:

```bash
git merge [nombre del remoto]/[rama remota]
```

También está la opción de combinar las dos acciones con el comando:

```bash
git pull
```

Si queremos que sea rebase, ejecutaremos lo siguiente (ya que por defecto hace un merge):

```bash
git pull --rebase
```

Para guardar los cambios no confirmados temporalmente, usaremos:

```bash
git stash save
```

Si queremos restaurar los cambios guardados en el stash, ejecutaremos:

```bash
git stash pop
```

Hay un comando que hace los tres comandos a la vez:

```bash
git pull --autostash
```

---

## Colaboración en GitHub

Para crear la rama remota que hace referencia al repositorio original, se utiliza el siguiente comando:

```bash
git remote add upstream [enlace github]
```

Si queremos enviar una rama al repositorio origin, tendremos que ejecutar:

```bash
git push --set-upstream origin [nombre de nuestra rama]
```

---
```