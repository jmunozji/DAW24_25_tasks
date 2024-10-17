---
title: 'CH1 - Pere Josep Prior España'
---

# **Cheatsheet UD1 - Despliegue de Aplicaciones**

Este documento resume los comandos esenciales de **Git** y **GitHub** utilizados en la línea de comandos Linux, principalmente para la **Unidad 1** del curso de **Despliegue de Aplicaciones**.

---

## **Instalación de Git**

### **Ubuntu/Debian**
```bash
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install git
```

- Actualiza la lista de paquetes, actualiza los paquetes instalados y luego instala Git.

### **Fedora**
```bash
$ sudo yum install git
```

- Instala Git en distribuciones Fedora.


## **Configuración Inicial**

### **Configuración del Usuario**

```bash
$ git config --global user.name "Tu Nombre"
$ git config --global user.email usuario@example.com
```

- Configura tu nombre y correo electrónico para que Git los asocie con tus commits.

### **Configuración de Push y Branch**

```bash
$ git config --global push.default simple
$ git config --global init.defaultBranch main
```

- Define el comportamiento por defecto del comando git push y establece la rama principal como main.

### **Configuración de Exclusiones**

```bash
$ git config --global core.excludesfile $HOME/.gitignore_global
```

- Establece un archivo .gitignore global para excluir archivos específicos en todos tus proyectos.

---

## **Comandos Básicos**

### **Estado y Preparación**
```bash
$ git status
```

- Muestra el estado del repositorio.

```bash
$ git init
```

- Inicializa un nuevo repositorio Git.

```bash
$ git add <file-name>
$ git add .
```

- Añade archivos al área de preparación.

```bash
$ git commit -m "Creación del proyecto"
$ git commit -am "Creación del proyecto"
```

- Crea un commit con los cambios añadidos al area de preparación

### **Historial de Cambios**

```bash
$ git log
$ git log --oneline
$ git log --oneline --max-count=2
$ git log --oneline --since='5 minutes ago'
$ git log --oneline --until='5 minutes ago'
$ git log --oneline --author=usuario
$ git log --oneline --all
$ git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```

- Revisa el historial de commits con diferentes formatos y filtros.

### **Alias Útiles**

```bash
$ git config --global alias.br branch
$ git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
```

- Crea alias para acceder fácilmente a comandos recurrentes como branch y log con formato largo.

### **Manejo de Etiquetas**

```bash
$ git tag
$ git tag <version-name>
$ git tag -d <tag-name>
```

- Lista, crea o elimina etiquetas (tags) a las versiones del proyecto.

### **Mostrar diferencias**

```bash
$ git diff
$ git diff --staged
```

- Muestra las diferencias entre el directorio de trabajo y el área de preparación.

```bash
$ git diff <branch-name>
$ git diff <current-tag> <diff-tag>
```

- Muestra las diferencias entre el dictorio de trabajo y una rama, o entre dos etiquetas.

---

## **Comandos Avanzados**

```bash
$ git cherry-pick <commit-hash>
```

- Aplica los cambios de un commit específico en la rama actual.

### **Restaurar Cambios**
```bash
$ git add <file-name>
$ git restore --staged <file-name>
```

- Restaura archivos del área de preparación.

```bash
$ git restore <file-name>
```

- Restaura archivos del directorio de trabajo.

```bash
$ git commit -am "Commit mal"
$ git revert HEAD --no-edit
```

- Revierte el último commit realizado.

```bash
$ git reset --hard <branch-tag>
```

- Resetea el repositorio al estado de una rama o etiqueta específica.

```bash
$ git commit --amend -m "Añadido el autor del programa y su email"
```

- Modifica el contenido del último commit.

## **Mover y Eliminar Archivos**

```bash 
$ git mv <old-file> <new-file>
$ git rm <file-name>
```

- Mueve o elimina archivos en el repositorio.

---

## **Comandos de Ramas**

### **Gestión de Ramas**

```bash
$ git branch
$ git branch -r
```

- Listar las ramas locales, y las remotas.

```bash
$ git branch <branch-name>
$ git branch -d <branch-name>
```

- Crear y eliminar una rama.

```bash
$ git checkout <branch-name>
$ git checkout -b <branch-name>
```

- Cambiar a una rama específica, o crea y cambia a una nueva rama

```bash
$ git switch -
$ git switch -c <new-branch-name>
```

- Cambia a la rama anterior, o crea y cambia a una nueva rama.

### **Fusionar Ramas**

```bash
$ git merge <branch-name>
$ git merge -m "Nombre commit" --no-ff <branch-name>
```

- Fusiona una rama en la actual. Usa --no-ff para conservar el historial de la rama fusionada.


```bash
$ git rebase <branch-name>
$ git rebase --continue
```

- Integra cambios de una rama en otra reescribiendo el historial.

---

## **Comandos para Trabajar en Remoto**

### **Generar Clave SSH**
```bash
$ ssh-keygen -t rsa -C "Cuenta GitHub"
```

- Genera una clave SSH para autenticarse en GitHub.

### **Clonar Repositorio**

```bash
$ git clone git@github.com:github-user/repository-name.git
$ cd repository-name/
```

- Clona un repositorio de GitHub y navega al directorio del mismo.

### **Configuración Remota**

```bash
$ git remote add origin git@github.com:github-user/repository-name.git
$ git branch -M main
$ git push -u origin main
```

- Añade un repositorio remoto, cambia el nombre de la rama principal y envía los cambios al Hub.

```bash
$ git remote show origin
```

- Muestra información sobre el repositorio remoto.

### **Sincronización con Remoto**

```bash
$ git fetch
$ git remote update
```

- Descarga objetos y referencias desde el repositorio remoto.

```bash
$ git push
$ git pull
```

- Envía y recibe de cambios desde el repositorio remoto.

### **Caja de cambios**

```bash
$ git stash save
$ git pull
$ git stash pop
```

- Guarda cambios no confirmados en el stash, recibe la nueva información y aplica los cambios guardados en el stash.

```bash
$ git stash list
```

- Listar los cambios guardados en el stash.

```bash
$ git stash drop
```

- Eliminar los cambios guardados en el stash.

```bash
$ git pull --autostash
$ git pull --autostash --rebase
```

- Recibe de cambios del remoto y guarda automáticamente los cambios no confirmados en el stash.

### **Gestión de ramas remotas**

```bash
$ git push --set-upstream origin <branch-name>
```

- Empuja una rama y establece la rama en el directorio remoto.

```bash
$ git merge --squash <branch-name>
```

- Fusiona los commits de una rama en un solo commit.

```bash
$ git push origin --delete add-license
```

- Envíar los cambios y eliminar la rama remota.