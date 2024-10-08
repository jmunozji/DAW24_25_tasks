# 📝 UD1 - Git Cheatsheet

> David Maratrat Pons 

## 📁 Instalación de Git

| Comando                           | Descripción                                 |
|-----------------------------------|---------------------------------------------|
| **🐧 Linux**                      |                                             |
| `apt-get install git`             | Instalación de git en distribuciones Debian |
| `yum install git-core`            | Instalación de git por instalador binario   |
| **🪟  Windows**                   |                                             |
| [https://gitforwindows.org/](URL) | URL de descarga de git                      |
| **🍏 MacOS**                      |                                             |
| `brew install git`                | Instalación de gitpara MacOS                |

---

## ⚙️ Configuración de Git

| Comando                                                         | Descripción                                                       |
|-----------------------------------------------------------------|-------------------------------------------------------------------|
| `git config --global user.name "nombre"`                        | Configurar el nombre de usuario a nivel global                    |
| `git config --global user.email "example@correo.com"`           | Configurar el correo electrónico a nivel global                   |
| `git config --global push.default simple"`                      | Define estrategia por defecto al hacer git push                   |
| `git config --global core.excludesfile $HOME/.gitignore_global` | Excluir archivos/directorios dentro del archivo .gitignore_global |

---

## 🗃️ Uso básico de Git

| Comando                        | Descripción                                           |
|--------------------------------|-------------------------------------------------------|
| `git init`                     | Inicializa el repositorio git en la carpteta actual   |
| `git status`                   | Permite ver el estado de los archivos del repositorio |
| `git add .`                    | Añadir todos los archivos al staging area             |
| `git add "archivo/carpeta"`    | Añade el archivo/carpeta al staging area              |
| `git commit -m "mensaje"`      | Confirmar los cambios con un mensaje de commit        |
| `git push origin <rama>`       | Enviar los cambios al repositorio remoto              |
| `git checkout "xxxxxxx"`       | Cambia el HEAD de versión usando el hash acortado     |
| `git switch -`                 | Cambia el HEAD al la última versión                   |

---

## 🌿 Ramas

| Comando                        | Descripción                                                            |
|--------------------------------|------------------------------------------------------------------------|
| `git branch`                   | Listar todas las ramas locales                                         |
| `git branch "nombre"`          | Crea una rama nueva                                                    |
| `git checkout "nombre"`        | Cambiar a una rama específica                                          |
| `git checkout -b <nueva-rama>` | Crear y cambiar a una nueva rama                                       |
| `git branch -d <rama>`         | Eliminar una rama local                                                |
| `git merge <rama>`             | Fusionar otra rama en la rama actual                                   |
| `git merge <rama> --no-ff`     | Fusionar otra rama en la rama actual guardando el historial de cambios |
| `git rebase <rama>`            | Aplica los commits de la rama actual sobre otra                        |

---

## 🏷️ Etiquetado

| Comando                        | Descripción                                           |
|--------------------------------|-------------------------------------------------------|
| `git tag v1`                   | Asigna una tiqueta a una versión                      |
| `git tag -d "nombre_etiqueta"` | Elimina una etiqueta                                  |

---

## ↩️ Restaurado

| Comando                          | Descripción                                                                   |
|----------------------------------|-------------------------------------------------------------------------------|
| `git restore "archivo"`          | Restaura los cambios de un archivo antes de haber hecho un `git add`          |
| `git restore --staged "archivo"` | Devuelve el archivo al Working Directory                                      |
| `git revert HEAD --no-edit`      | Crea un nuevo commit deshaciendo los cambios realizados en el commit anterior |
| `git reset --hard "xxxxxxx"`     | Devuelve el HEAD a un commit anterior y borra los cambios posteriores         |

---

## ⏳ Historial y Versionado

| Comando                                                                                            | Descripción                                                |
|----------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| `git config --global alias.hist "log --pretty=format:'%h %ad \| %s%d [%an]' --graph --date=short"` | Crea un alias para ver los cambios formatados              |
| `git log`                                                                                          | Permite ver el historial de commits                        |
| `git log --oneline`                                                                                | Permite ver los cambios realizados en una linea            |
| `git show "xxxxxxx"`                                                                               | Permite ver los detalles de un commit en específico        |
| `git remote show origin`                                                                           | Podemos ver la configuración de este repositorio remoto    |
| `git diff`                                                                                         | Permite ver los cambios entre commits o ramas              |

---

## 🌐 Remoto

| Comando                                                   | Descripción                                                                                             |
|-----------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| `git clone git@github.com:tu/repositorio.git`             | Clona un repositorio en local del remoto                                                                |
| `git remote add origin git@github.com:tu/repositorio.git` | Asocia tu repositorio local con el remoto                                                               |
| `git branch -M main`                                      | Cambia el nombre de la rama actual a main (compatibilidad github)                                       |
| `git push -u origin main`                                 | Envia los cambios de main a origin y establece origin/main como rama por defecto para futuros pull/push |
| `git merge <origin/main>`                                 | Fusionar una rama del remoto en la rama local actual                                                    |
| `git rebase <origin/main>`                                | Aplica los commits de una rama del remoto en la rama local actual                                       |
| `git rebase --continue `                                  | Continua con el rebase después de solucionar un conflicto                                               |

---

## 🔄 Actualización y Sincronización

| Comando                           | Descripción                                                     |
|-----------------------------------|-----------------------------------------------------------------|
| `git pull origin <rama>`          | Descargar y fusionar cambios desde el remoto                    |
| `git pull --rebase origin <rama>` | Descargar y fusionar (mediante rebase) cambios desde el remoto  |
| `git push origin main`            | Envia los cambios locales de la rama main al repositorio origin |
| `git fetch`                       | Traer los cambios remotos sin fusionar                          |
| `git merge <rama>`                | Fusionar otra rama en la rama actual                            |

---

## 🔀 Git Flow

| Comando                                 | Descripción                                            |
|-----------------------------------------|--------------------------------------------------------|
| `git flow init`                         | Inicializa el espacio de trabajo                       |
| `git flow feature start feature_branch` | Crea una rama de funcionalidad                         |
| `git flow feature stop feature_branch`  | Elimina una rama de funcionalidad                      |
| `git flow release`                      | Crea la rama de publicación de versiones               |
| `git flow hotfix start hotfix_branch`   | Crea una rama para parchear errores de las releases    |
| `git flow hotfix finish hotfix_branch`  | Cierra una rama de parchedo de errores de las releases |


---

## 📥 Stash

| Comando            | Descripción                                               |
|--------------------|-----------------------------------------------------------|
| `git stash`        | Guardar temporalmente los cambios sin hacer commit        |
| `git stash save`   | Guarda los cambios sin hacer commit                       |
| `git stash apply`  | Recupera el cambio guardado más reciente                  |
| `git stash pop`    | Recuperar los cambios guardados                           |
| `git stash list`   | Muestra los cambios guardados                             |
| `git stash show`   | Muestra un resumen de los ficheros modificados en la pila |
| `git stash branch` | Permite crear una nueva rama a partir del último stash    |
| `git stash clear`  | Borra todos los stash de la pila                          |
| `git stash drop`   | Permite borrar un unico stash de la pila                  |


---

## 🌳 Worktree

| Comando                                      | Descripción                                                       |
|----------------------------------------------|-------------------------------------------------------------------|
| `git worktree add /directorio master`        | Crea un directorio de trabajo temporal con el contenido de master |
| `git worktree add -b fix /directorio master` | Crea la rama fix desde master                                     |
| `git worktree list`                          | GMuestra el listado de directorios y espacios de trabajo          |
| `git worktree delete fix`                    | Borra un espacio de trabajo                                       |

---

## 🕵️ Blame

| Comando             | Descripción                                                                                               |
|---------------------|-----------------------------------------------------------------------------------------------------------|
| `git blame archivo` | Permite saber quién fue el último en modificar una línea concreta de código, en qué commit y en qué fecha |

---
## 🧰 Otras Utilidades

| Comando                                | Descripción                                             |
|----------------------------------------|---------------------------------------------------------|
| `git rm <archivo>`                     | Eliminar un archivo del repositorio y del directorio    |
| `git mv <archivo-nuevo>`               | Renombrar o mover un archivo                            |
| `git rebase <rama>`                    | Aplicar commits de una rama sobre otra                  |
| `ssh-keygen -t rsa -C "Cuenta GitHub"` | Crea par de claves para poder usar github con ssh       |
| `Closes #1`                            | Cierra una incidéncia desde la descripción de un commit |

---

## 🔍 Ayuda y Documentación

| Comando                | Descripción                                     |
|------------------------|-------------------------------------------------|
| `git help <comando>`   | Ver la ayuda detallada de un comando específico |
| `git --version`        | Ver la versión de Git instalada                 |

