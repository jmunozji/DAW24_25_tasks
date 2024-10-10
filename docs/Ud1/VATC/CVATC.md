---

title: 'Cheatsheet 1'

---

# 🌟 UD1 - Cheatsheet

> ✍️ **Victor Alexandru Teleanu Covaci**

## 🚀 Instalación de Git

| **💻 Sistema Operativo / 🛠️Comando**             | **📝 Descripción**                            |
|---------------------------------------------------|-----------------------------------------------|
| **🐧 Linux**                                      |                                               |
| `yum install git-core`                            | Instalar Git en Linux (instalador binario)    |
| `apt-get install git`                             | Instalar Git en distribución basada en Debian |
| **🪟 Windows**                                    |                                               |
| [🔗 Git para Windows](https://gitforwindows.org/) | Descargar Git para Windows                    |
| **🍏 MacOS**                                      |                                               |
| `brew install git`                                | Instalar Git en MacOS                         |

### 🔍 **Recomendación**

- Con `git version` puedes comprobar la versión instalada de Git.
- Con `git help <comando>` podemos ver ayuda detallada sobre el comando deseado.

## ⚙️ Configuración Git

### 🧑‍💻 **Tu identidad**

| **🛠️ Comando**                                                  | **📝 Descripción**                                                  |
|------------------------------------------------------------------|---------------------------------------------------------------------|
| `git config --global user.name "John Doe"`                       | Establece tu nombre de usuario en la configuración global de git    |
| `git config --global user.email johndoe@example.com`             | Establece tu correo electrónico en la configuración global de git   |
| `git config --global push.default simple`                        | Envía solo la rama actual al repositorio remoto con el mismo nombre |
| `git config --global core.excludesfile $HOME/.gitignore_global`  | Indicamos a git que queremos tener un fichero gitignore global      |

## 📚 Uso básico de Git

| **🛠️ Comando**                      | **📝 Descripción**                                                                 |
|--------------------------------------|------------------------------------------------------------------------------------|
| `git init`                           | Crea un nuevo repositorio                                                          |
| `git status`                         | Consulta el estado de los archivos del proyecto                                     |
| `git add archivo.txt`                | Almacena el archivo en el repositorio; pasa a la "Staging area"                   |
| `git add .`                          | Añade todos los archivos y directorios al proyecto                                 |
| `git commit -m "mensaje del commit"` | Guarda los cambios permanentemente en el repositorio con un mensaje de commit       |
| `git push origin <rama>`             | Envía los cambios de la rama local al repositorio remoto                           |
| `git switch <rama>`                  | Cambia a la rama especificada                                                      |

### 📁 Estados de un archivo en Git

- **Sin seguimiento (untracked)**: Archivos que no están siendo rastreados por Git.
- **Modificado (modified)**: Archivos que han cambiado desde el último commit.
- **Preparado (staged)**: Archivos que están listos para ser confirmados.
- **Confirmado (committed)**: Archivos que han sido guardados en el historial del repositorio.

## 📜 Historial

| **🛠️ Comando**                                                                                   | **📝 Descripción**                                        |
|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| `git log`                                                                                         | Muestra todos los cambios realizados                      |
| `git log --oneline`                                                                               | Muestra el historial en una sola línea                   |
| `git config --global alias.hist "log --pretty=format:'%h %ad / %s%d [%an]' --graph --date=short"` | Crea un alias para ver el historial de forma personalizada |
| `git hist`                                                                                        | Ejecuta el alias creado                                   |
| `git diff "v1" "v2"`                                                                              | Muestra los cambios entre versiones                       |

## 📦 Versionado

| **🛠️ Comando**              | **📝 Descripción**                     |
|------------------------------|----------------------------------------|
| `git tag`                    | Muestra todas las etiquetas existentes |
| `git tag v1`                 | Etiqueta una versión específica        |
| `git tag -d nombre_etiqueta` | Borra la etiqueta especificada         |

## Deshacer cambios

| **🛠️ Comando**                    | **📝 Descripción**                                                                                                                             |
|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `git restore archivo.txt`          | Revertirá los cambios a la última versión que se encuentra en el último commit                                                                 |
| `git restore --staged archivo.txt` | Sacamos el archivo del Staging area y pasa al Working directory                                                                                |
| `git revert HEAD --no-edit`        | Crea un nuevo commit que deshace los cambios realizados en ese commit. El parámetro"--no-edit" indica que use el mensaje de commit por defecto |
| `git reset --hard v1`              | Revierte un commit sin dejar huella en el historial                                                                                            |

## Ramas

| **🛠️ Comando**                 | **📝 Descripción**                                                       |
|---------------------------------|--------------------------------------------------------------------------|
| `git branch`                    | Vemos todas las ramas                                                    |
| `git branch nombre_rama`        | Creamos una nueva rama                                                   |
| `git checkout nombre_rama`      | Cambiar a rama específica                                                |
| `git checkout -b <nombre_rama>` | Crea y cambia a nueva rama                                               |
| `git merge <rama>`              | Incorporar cambios de una rama a la actual sin dejar rastro de la fusión |
| `git merge <rama> --no-ff`      | Incorporar cambios de una rama a la actual dejando rastro de la fusión   |
| `git rebase <rama>`             | Tomar los commits de una rama y los aplica a otra                        |

## Github

| **🛠️ Comando**                                    | **📝 Descripción**                                 |
|----------------------------------------------------|----------------------------------------------------|
| `git clone git@github.com:usario/repositorio.git`  | Clona un repositorio remoto para trabajar en local |
| `git remote`                                       | Ver configuración del repositorio remoto           | 





