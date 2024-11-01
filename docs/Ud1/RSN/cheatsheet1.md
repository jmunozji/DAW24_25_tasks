---
title: 'CH1 - Raul Simarro Navarro'
---

# Cheatsheet git

### Uso básico de Git

- git status -> Saber el estado de los archivos(dónde se encuentran)

- git init -> Inicializar git(únicamente se debe realizar una vez)

- git add -> Añadir a Staging area

- git commit -m "Nombre del commit" -> Pasar al repositorio local

- .gitignore -> Archivo que se crea para ignorar archivos

- git config --global core.excludesfile $HOME/.gitignore_global -> Fichero gitignore global

- git log -> Observar todos los cambios realizados anteriormente

### Git log

1. git log --online --max-count=2 -> Muestra máximo 2 lineas

2. git log --oneline --since='5 minutes ago' -> Muestra a partir de 5 min

3. git log --oneline --until='5 minutes ago' -> Muestra hasta 5 min.

4. git log --oneline --author=rauls -> Muestra los del autor

5. git log --oneline --all -> Muestra todos

6. git log --online -> Muestra únicamente una línea

7. git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short -> Nos permite ver dónde estan master y HEAD y más

### Crear alias

- git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short" -> Nos perimte crear un alias para comandos

- git hist -> Se ejecuta el alias

- git hist master --all -> Muestra todo el historial

### Checkout

- git checkout "hash" -> Se utiliza para regresar al estado del proyecto

- git switch - -> Vuelve al estado final

- git checkout "tag"^ -> Nos lleva al ancestro

### Tag

- git tag -> Crea una etiqueta concreta

- git tag -d "etiqueta" -> Borra la etiqueta

### Ver cambios

- git diff "nombre etiqueta"-"nombre etiqueta" -> Ver cambios que se han realizado

### Deshacer cambios

- git restore "nombre archivo" -> Deshacer cambios

-git restore --staged "nombre archivo" -> Sacar del área de staging

### Deshacer commits

- git revert HEAD --no-edit -> Revertir último commit que apunta a HEAD

### Borar commits de una rama

- git reset --hard "nombre" -> Eliminar commit

### Modificar commit

- git commit --amend -m "Descripcion"-> Permite modificar el último estado confirmado

### Mover y borrar archivos

- git mv "Nombre" "Carpeta"-> Mover a la carpeta
- git rm "Nombre" -> Eliminar archivo 


# Ramas

- git branch "Nombre" -> Crear rama

- git checkout "Nombre" -> Moverse a la rama

### Fusion de ramas

- git merge "Nombre" -> Se ejecuta en la rama cuyos cambios queremos incorprar a la actual

- git rebase "Nombre rama" -> Es otra forma de fusionar

(Se recomienda el uso de --no-ff, para dejar rastro de fusión)

# Github

###Creación de clave ssh

- ssh-keygen -t rsa -C "Cuenta GitHub" -> Crea una clave ssh

### Comandos para repositorio en la nube

- git remote add origin "Enlace ssh github" -> Añade un repositorio remoto

- git branch -M main -> Se utiliza para renombrar a una rama

- git push -u origin main -> Envia los cambios de locala la nube

- git clone "Enlace github" -> Clona el repositorio en local

- git fetch -> Trae información del remoto

- git pull -> Descarga el contenido del remoto y lo fusiona con la rama local

- git stash save -> Se guardan los cambios en la pila

- git stash pop -> Se sacan los cambios de la pila

