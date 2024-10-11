# Cheatsheet git Marc Ibáñez

## Uso básico de git

### Instalación de github
    $ apt-get install git

### Definir identidad
    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com

### Ver estado
    $ git status

### Crear un proyecto
    $ mkdir curso-de-git
    $ cd curso-de-git

### Crear repositorio
    $ git init

### Añadir y commitear los cambios
    $ git commit -am "Nombre del commit"

### Ignorar archivos globalmente
    $ git config --global core.excludesfile $HOME/.gitignore_global

### Ver historial
    $ git log

    $ git log --oneline

### Crear alias para ver el historial
    $ git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"

### Ver historial una vez hecho el alias
    $ git hist

### Recuperar versiones
    $ git checkout 0000001

##### Para volver
    $ git switch -

##### Volver a ultima versión de la rama
    $ git checkout master

### Etiquetar versiones

##### Ver etiquetas
    $ git tag

##### Etiquetar versión actual
    $ git tag v1

##### Etiquetar versión inmediatamente anterior
    $ git checkout v1^
    $ git tag v1-beta

##### Borrar etiqueta
    $ git tag -d nombre_etiqueta


## Uso avanzado de git

### Deshacer cambios

##### Después de commit
    $ git restore hola.php

##### Antes de commit
    $ git restore --staged hola.php

### Deshacer commits indeseados
    $ git revert HEAD --no-edit

### Borrar commits de una rama
    $ git reset --hard v1

### Modificar un commit
    $ git add hola.php
    $ git commit --amend -m "Añadido el autor del programa y su email"

### Mover archivo a otro directorio
    $ mkdir lib
    $ git mv hola.php lib

## Ramas

### Crear rama
    $ git branch nuevarama

### Moverse entre ramas
    $ git checkout nuevarama

### Mezclar ramas

##### Hacemos modificación en rama master
    $ git checkout master
    $ git add README.md
    $ git commit -m "Añadido README.md a master"

###### Cambiamos a rama nuevarama y hacemos la mezcla
    $ git checkout nuevarama
    $ git merge master

### Resolver conflictos

##### Modificamos el archivo hola.php estando en rama master, guardamos los cambios y hacemos commit, y una vez estamos en la rama nuevarama hacemos merge:
    $ git merge master
    Auto-merging lib/hola.php
    CONFLICT (content): Merge conflict in lib/hola.php
    Automatic merge failed; fix conflicts and then commit the result.

##### Ahora debemos editar el documento para resolver los conflictos y una vez resuelto hacemos:
    $git add lib/hola.php
    $ git commit -m "Solucionado el conflicto al fusionar con la rama master"
    [nuevarama a36af04] Solucionado el conflicto al fusionar con la rama master

## Github

### Sincronizar un repositorio ya creado
    $ git remote add origin git@github.com:sgomez/taller-de-git.git
    $ git branch -M main
    $ git push -u origin main

### Clonar un repositorio
    $ git clone git@github.com:sgomez/taller-de-git.git
    $ cd taller-de-git

### Ramas remotas
    $ git remote show origin

### Enviar actualizaciones 
    $ git push

### Recibir actualizaciones
    $ git fetch

### Importar cambios de la rama remota a la local
    $ git merge origin/main

#### Existe una funcion que nos ayuda a hacer fetch y merge de forma simultanea:
    $ git pull

### Problemas de sincronización
#### No puedo hacer push:
    $ git fetch
    $ git rebase
#### O también:
    $ git pull --rebase

#### No puedo hacer pull:
    $ git stash save # Guardamos los cambios en la pila
    $ git pull # Sincronizamos con el repositorio remoto, -r para hacer rebase puede ser requerido
    $ git stash pop # Sacamos los cambios de la pila
