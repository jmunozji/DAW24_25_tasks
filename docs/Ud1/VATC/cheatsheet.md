---

title: 'Cheatsheet 1'

---

# 🌟 UD1 - Cheatsheet

> ✍️ **Victor Alexandru Teleanu Covaci**

## 🚀 Instalación de Git

| **💻 Sistema Operativo / 🛠️ Comando**             | **📝 Descripción**                            |
|---------------------------------------------------|-----------------------------------------------|
| **🐧 Linux**                                      |                                               |
| `yum install git-core`                            | Instalar Git en Linux (instalador binario)    |
| `apt-get install git`                             | Instalar Git en distribución basada en Debian |
| **🪟 Windows**                                    |                                               |
| [🔗 Git para Windows](https://gitforwindows.org/) | Descargar Git para Windows                    |
| **🍏 MacOS**                                      |                                               |
| `brew install git`                                | Instalar Git en MacOS                         |

### 🔍 **Recomendaciones**

- Con `git version` puedes comprobar la versión instalada de Git.
- Con `git help <comando>` podemos ver ayuda detallada sobre el comando deseado.

## ⚙️ Configuración Git

### 🧑‍💻 **Tu identidad**

| **🛠️ Comando**                                                  | **📝 Descripción**                                                  |
|------------------------------------------------------------------|---------------------------------------------------------------------|
| `git config --global user.name "John Doe"`                       | Establece tu nombre de usuario global                               |
| `git config --global user.email johndoe@example.com`             | Establece tu correo electrónico global                              |
| `git config --global push.default simple`                        | Envía solo la rama actual al repositorio remoto                     |
| `git config --global core.excludesfile $HOME/.gitignore_global`  | Configura un fichero gitignore global                               |

## 📚 Uso básico de Git

| **🛠️ Comando**                      | **📝 Descripción**                                                                 |
|--------------------------------------|------------------------------------------------------------------------------------|
| `git init`                           | Crea un nuevo repositorio                                                          |
| `git status`                         | Consulta el estado de los archivos del proyecto                                    |
| `git add archivo.txt`                | Almacena el archivo en el repositorio; pasa a la "Staging area"                    |
| `git add .`                          | Añade todos los archivos y directorios al proyecto                                 |
| `git commit -m "mensaje del commit"` | Guarda los cambios en el repositorio con un mensaje de commit                      |
| `git push origin <rama>`             | Envía los cambios de la rama local al repositorio remoto                           |
| `git switch <rama>`                  | Cambia a la rama especificada                                                      |

### 📁 Estados de un archivo en Git

- **Sin seguimiento (untracked)**: Archivos no rastreados por Git.
- **Modificado (modified)**: Archivos con cambios desde el último commit.
- **Preparado (staged)**: Archivos listos para ser confirmados.
- **Confirmado (committed)**: Archivos guardados en el historial del repositorio.

## 📜 Historial

| **🛠️ Comando**                                                                                   | **📝 Descripción**                                         |
|---------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| `git log`                                                                                         | Muestra todos los cambios realizados                       |
| `git log --oneline`                                                                               | Muestra el historial en una sola línea                     |
| `git config --global alias.hist "log --pretty=format:'%h %ad / %s%d [%an]' --graph --date=short"` | Crea un alias para ver el historial de forma personalizada |
| `git hist`                                                                                        | Ejecuta el alias creado                                    |
| `git diff "v1" "v2"`                                                                              | Muestra los cambios entre versiones                        |

## 📦 Versionado

| **🛠️ Comando**              | **📝 Descripción**                     |
|------------------------------|----------------------------------------|
| `git tag`                    | Muestra todas las etiquetas existentes |
| `git tag v1`                 | Etiqueta una versión específica        |
| `git tag -d nombre_etiqueta` | Borra la etiqueta especificada         |

## 🛠️ Deshacer cambios

| **🛠️ Comando**                    | **📝 Descripción**                                                                |
|------------------------------------|-----------------------------------------------------------------------------------|
| `git restore archivo.txt`          | Revierte los cambios a la última versión que se encuentra en el último commit     |
| `git restore --staged archivo.txt` | Mueve el archivo del Staging Area al Working Directory                            |
| `git revert HEAD --no-edit`        | Crea un nuevo commit que deshace los cambios realizados en el commit actual       |
| `git reset --hard v1`              | Revierte un commit sin dejar rastro en el historial                               |

## 🌿 Ramas

| **🛠️ Comando**                 | **📝 Descripción**                                                        |
|---------------------------------|---------------------------------------------------------------------------|
| `git branch`                    | Lista todas las ramas                                                     |
| `git branch nombre_rama`        | Crea una nueva rama                                                       |
| `git checkout nombre_rama`      | Cambia a una rama específica                                              |
| `git checkout -b <nombre_rama>` | Crea y cambia a una nueva rama                                            |
| `git merge <rama>`              | Incorpora los cambios de una rama a la actual sin dejar rastro de fusión  |
| `git merge <rama> --no-ff`      | Incorpora cambios de una rama a la actual dejando rastro de fusión        |
| `git rebase <rama>`             | Aplica los commits de una rama a otra                                     |

## 🌐 GitHub

| **🛠️ Comando**                                    | **📝 Descripción**                                               |
|----------------------------------------------------|------------------------------------------------------------------|
| `git clone git@github.com:usuario/repositorio.git` | Clona un repositorio remoto en local                             |
| `git remote`                                       | Muestra la configuración del repositorio remoto                  | 
| `git fetch`                                        | Trae la información del servidor remoto                          |
| `git pull <remoto> <rama>`                         | Actualiza el repositorio local con los cambios del remoto        |
| `git pull --rebase <remoto> <rama>`                | Actualiza el repositorio local con rebase desde el remoto        |

## 🌀 Git Flow

| **🛠️ Comando**       | **📝 Descripción**                                    |
|-----------------------|-------------------------------------------------------|
| `git flow init`       | Inicializa el espacio de trabajo                      |
| `git flow feature `   | Crea y trabaja con ramas de funcionalidades           |
| `git flow release `   | Crea y trabaja con ramas de versiones                 |
| `git flow hotfix `    | Crea y trabaja con ramas de parches                   |

## 📦 Git Stash

| **🛠️ Comando**    | **📝 Descripción**                                                        |
|--------------------|---------------------------------------------------------------------------|
| `git stash`        | Guarda temporalmente los cambios en el espacio de trabajo                 |
| `git stash save`   | Permite guardar con un mensaje específico                                 |
| `git stash list`   | Muestra la lista de elementos guardados en stash                          |
| `git stash apply`  | Aplica el stash más reciente en el espacio de trabajo                     |
| `git stash pop`    | Aplica y elimina el stash más reciente                                    |
| `git stash show`   | Muestra un resumen de los archivos modificados en ese stash               |
| `git stash branch` | Crea una nueva rama desde el último stash                                 |
| `git stash clear`  | Elimina todos los elementos guardados en stash (acción destructiva)       |
| `git stash drop`   | Elimina un stash específico o el más reciente si no se indica ninguno     |

## 🌳 Git Worktree

| **🛠️ Comando**                       | **📝 Descripción**                                      |
|---------------------------------------|---------------------------------------------------------|
| `git worktree add /directorio master` | Crea un directorio con el estado de la rama master      |
| `git worktree list`                   | Muestra la lista de directorios y espacios de trabajo   |
| `git worktree delete fix`             | Elimina un espacio de trabajo                           |

## 🔍 Git Blame

| **🛠️ Comando**         | **📝 Descripción**                                                             |
|-------------------------|--------------------------------------------------------------------------------|
| `git blame archivo.txt` | Muestra quién modificó una línea específica de código, en qué commit y cuándo  |
