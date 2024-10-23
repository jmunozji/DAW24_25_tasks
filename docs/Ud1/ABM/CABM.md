---
title: 'CH1 - Andreu Beltran Miralles'
---

# Git Cheatsheet - Andreu Beltran Miralles

## Instalación y configuraciones globales

- **Instalar Git:**
  ```bash
  apt-get install git
  ```
  Instala Git en el sistema.

- **Configurar el nombre de usuario global:**
  ```bash
  git config --global user.name "John Doe"
  ```
  Configura el nombre de usuario global para los commits.

- **Configurar el email global:**
  ```bash
  git config --global user.email johndoe@example.com
  ```
  Configura el correo electrónico global para los commits.

- **Configurar el push simple por defecto:**
  ```bash
  git config --global push.default simple
  ```
  Configura que el `git push` se realice de manera simplificada.

- **Configurar un archivo `.gitignore` global:**
  ```bash
  git config --global core.excludesfile $HOME/.gitignore_global
  ```
  Especifica un archivo global `.gitignore` para excluir ciertos archivos en todos los proyectos.


## Estado de Git e historial

- **Ver el estado actual del repositorio:**
  ```bash
  git status
  ```
  Muestra el estado de los archivos en el repositorio (cambios, staged, untracked, etc.).

- **Ver el historial de commits:**
  ```bash
  git log
  ```
  Muestra los commits anteriores en el proyecto.

- **Crear un alias para un historial de commits formateado:**
  ```bash
  git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
  ```
  Crea un alias llamado `hist` que muestra el historial con formato gráfico y más información concisa.

  - **Usar el alias:**
    ```bash
    git hist
    ```
    Muestra el historial con el formato personalizado.


## Creación y adición

- **Inicializar un repositorio Git:**
  ```bash
  git init
  ```
  Crea un nuevo repositorio de Git en el directorio actual.

- **Añadir un archivo al área de staging:**
  ```bash
  git add hola.php
  ```
  Mueve el archivo `hola.php` al área de staging para ser incluido en el próximo commit.

- **Hacer un commit de los cambios:**
  ```bash
  git commit
  ```
  Crea un commit con los cambios actualmente en el área de staging.


## Eliminación

- **Eliminar un archivo y registrarlo en el próximo commit:**
  ```bash
  git rm hola.php
  ```
  Elimina `hola.php` del repositorio y lo añade al área de staging.


## Deshacer cambios

- **Restaurar los cambios de un archivo:**
  ```bash
  git restore hola.php
  ```
  Deshace los cambios locales en `hola.php`.

- **Eliminar un archivo del área de staging (sin eliminarlo del repositorio):**
  ```bash
  git restore --staged hola.php
  ```
  Retira `hola.php` del área de staging pero mantiene los cambios locales.

- **Deshacer el último commit sin modificar los archivos:**
  ```bash
  git revert HEAD --no-edit
  ```
  Crea un commit que deshace los cambios realizados en el commit anterior.

- **Restaurar el estado de un commit anterior:**
  ```bash
  git reset --hard v1
  ```
  Restaura el estado del repositorio al commit etiquetado como `v1`, eliminando cualquier cambio posterior.


## Etiquetas

- **Crear una etiqueta:**
  ```bash
  git tag v1
  ```
  Crea una etiqueta llamada `v1` en el commit actual.

- **Eliminar una etiqueta:**
  ```bash
  git tag -d v1
  ```
  Elimina la etiqueta `v1`.

- **Comparar dos etiquetas:**
  ```bash
  git diff v1-beta v1
  ```
  Muestra las diferencias entre las etiquetas `v1-beta` y `v1`.


## Ramas

- **Crear una nueva rama:**
  ```bash
  git branch hola
  ```
  Crea una nueva rama llamada `hola`.

- **Cambiar a una rama:**
  ```bash
  git checkout hola
  ```
  Cambia a la rama `hola`.

- **Fusionar una rama con la rama actual:**
  ```bash
  git merge master
  ```
  Fusiona los cambios de la rama `master` en la rama actual.

- **Rebasar la rama actual sobre otra:**
  ```bash
  git rebase master
  ```
  Aplica los commits de la rama actual encima de la rama `master`.


## GitHub

- **Añadir un repositorio remoto:**
  ```bash
  git remote add origin git@github.com:sgomez/taller-de-git.git
  ```
  Añade un repositorio remoto llamado `origin`.

- **Renombrar la rama principal a `main`:**
  ```bash
  git branch -M main
  ```
  Renombra la rama actual a `main`.

- **Subir cambios al repositorio remoto:**
  ```bash
  git push -u origin main
  ```
  Sube los cambios de la rama `main` al repositorio remoto `origin`.

- **Clonar un repositorio:**
  ```bash
  git clone git@github.com:sgomez/taller-de-git.git
  ```
  Descarga una copia del repositorio remoto en el equipo local.

- **Mostrar información sobre el repositorio remoto:**
  ```bash
  git remote show origin
  ```
  Muestra detalles sobre el repositorio remoto `origin`.

- **Obtener datos del repositorio remoto sin fusionar:**
  ```bash
  git fetch
  ```
  Descarga los cambios del repositorio remoto sin aplicarlos a la rama actual.

- **Actualizar la rama actual con los cambios del repositorio remoto:**
  ```bash
  git pull
  ```
  Descarga y fusiona los cambios del repositorio remoto en la rama actual.