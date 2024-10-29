---
title: 'CH1 - Alexandru Claudiu Fatu'
---

# GitHub Cheatsheet

### 1. Comandos de instalación y configuración

| Comando | Explicación |
|----------|----------|
| apt-get install git| Comando para instalar github en nuestro equipo Ubuntu o Debian   |
| git config --global user.name "(tu usuario)"| Comando para establecer tu nombre de usuario|
| git config --global user.email (tu email) | Comando para establecer tu dirección de correo electrónico|
|git config --global push.default simple| Comando para configurar el comportamiento predeterminado de Git a la hora de hacer un push a un repositorio remoto|

### 2. Comandos de uso básico de Git

| Comando | Explicación |
|----------|----------|
|git status|Comando para consultar el estado de los archivos|
|git init|Comando para crear un nuevo repositorio|
|git add (nombre del archivo)| Comando para añadir el archivo al área de staging o Staging Area|
|git commit| Comando para confirmar los cambios y pasar el archivo de Staging Area a nuestro repositorio local|
|git config --global core.excludesfile $HOME/.gitignore_global|Comando para indicar a git que queremos tener un fichero de gitignore global|
|git log|Comando para ver todos los cambios que hemos hecho|
|git config --global alias.hist "log --pretty=format:'%h %ad \| %s%d [%an]' --graph --date=short"| Comando para crear un alias de un comando muy largo a uno más corto|
|git checkout (rama)| Comando para cambiar de rama|
|git tag (nombre de etiqueta)|Comando para etiquetar versiones concretas de un repositorio|
|git switch (rama)| Comando para cambiar de rama|
|git diff (etiqueta 1) (etiqueta 2)| Comando para ver las diferencias entre dos versiones|

### 3. Comandos de uso avanzado de Git

| Comando | Explicación |
|----------|----------|
|git restore (archivo)|Comando para restaurar archivos o cambios en nuestro repositorio|
|git revert |Comando para revertir un commit en específico|
|git reset|Comando para revertir un commit pero sin dejar huella en el historial de cambios|
|git mv (archivo) (nueva ubicacion)|Comando para mover arhchivos|
|git rm (archivo)|Comando para eliminar archivos de nuestro repositorio Git|

### 4. Comandos de ramas Git

| Comando | Explicación |
|----------|----------|
|git branch (nombre rama)| Comando para crear una rama nueva|
|git branch -d (nombre rama) | Comando para borrar una rama|
|git merge (nombre de rama)|Comando para incorporar los cambios de una rama a otra|
|git rebase| Comando para reaplicar commits de una rama sobre otra|


### 5. Comandos GitHub

| Comando | Explicación |
|----------|----------|
|$ ssh-keygen -t rsa -C "(Descripción)"|Comando para crear una nueva clave pública/privada|
|git remote add origin (Dirección del repositorio remoto)|Comando para conectar nuestro repositorio local con un repositorio remoto|
|git push|Comando para enviar los commits realizados en nuestro repositorio local a un repositorio remoto|
|git clone (Dirección del repositorio)|Comando para clonar un repositorio remoto a nuestro repositorio local|
|git remote |Comando para ver la configuración del respositorio|
|git fetch| Comando para descargar los cambios desde un repositorio remoto|
|git pull| Comando para describir y aplicar cambios desde un repositorio remoto a nuestra rama local actual|
|git stash save|Comando para guardar los cambios temporalmente en la pila|
|git stash pop| Comando para sacar los cambios de la pila|

### 6. Comandos de colaboración en GitHub

| Comando | Explicación |
|----------|----------|
|git remote add upstream (dirección del repositorio remoto)|Comando para añadir un repositorio adicional a nuestro repositorio local|
|git remote show upstream| Comando para mostrar el repositorio remoto|
