---
title: 'CH1 - Marcel Mañas Calafí'
---

# Cheatsheet 1 de Marcel Mañas


## Ayuda

Por si en algún momento necesitas ayuda

```bash
$ git help <verb>
$ git <verb> --help            Ej: $ git help config
$ man git <verb>;
```

---

## Instalación y configuración de Git

- Instalar en distribuciones de Debian:
    
    ```bash
    $ sudo apt-get install git
    ```
    
- Configuración:
    
    ```bash
    $git config --global user.name "Tu_nombre"
    $git config --global user.email "tu_email@aquí.algo"
    $git config --global push.default simple
    ```
    
- Comprobar versión de Git:
    
    ```bash
    $ git --version
    ```
    
- Actualizar Git:
    
    ```bash
    $ sudo apt update
    $ sudo apt upgrade git
    ```
    

---

## Creación de proyecto

- Ver el estado del repositorio:
    
    ```bash
    $ git status
    ```
    
- Crear repositorio una vez ya creado el directorio y el archivo:
    
    ```bash
    $ git init
    Initialized empty Git repository in /home/admin/nombre_del_directorio/.git/
    ```
    

---

## GitHub

- Para crear las claves públicas/privadas:
    
    ```bash
    $ ssh-keygen -t rsa -C "Cuenta GitHub"
    ```
    
- Para conectar Git con un repositorio existente GitHub:
    
    ```bash
    $ git remote add origin código_ssh_del_repositorio_GitHub
    ```
    
    - Para cambiar el nombre a una rama:
        
        ```bash
        $ git branch -M main
        ```
        
    - Para subir la info al repositorio remoto y decir que esa es la rama de seguimiento:
        
        ```bash
        $ git push -u origin main
        ```
        
- Para clonar un repositorio existente:
    
    ```bash
    $ git clone código_ssh_GitHub
    ```
    
- Para ver la configuración del repositorio remoto:
    
    ```bash
    $ git remote show origin
    ```
    
- Traer la información de los cambios en GitHub al local:
    
    ```bash
    $ git fetch
    ```
    
    - Luego tendrías que añadirlo a tu rama con:
        
        ```bash
        $ git merge origin/main
        ```
        
- Traer la información de los cambios en GitHub al local y añadirlos a tu rama:
    
    ```bash
    $ git pull
    ```
    
    - Un pull mas agresivo:
        
        ```bash
        $ git pull --rebase
        ```
        
- Guardar cambios de forma temporal:
    
    ```bash
    $ git stash save # Guardamos los cambios en la pila
    ```
    
- Sacar los cambios temporales que tengamos guardados:
    
    ```bash
    $ git stash pop # Sacamos los cambios de la pila
    ```
    
- Para utilizar stash save y stash pop, además de un pull:
    
    ```bash
    $ git pull --autostash
    ```
    

## Manejo en el repositorio Git

- Añadir el archivo al repositorio:
    
    ```bash
    $ git add hola.php
    ```
    
    - Si quieres añadir varios de golpe:
        
        ```bash
        $ git add .
        ```
        
- Guardar los archivo añadido al repositorio:
    
    ```bash
    $git commit -m "Comentario descriptivo del guardado"
    ```
    
    - Commit directo:
        
        ```bash
        $git commit -a -m "Añadido el autor del programa"
        ```
        
- Comprobar el histórico:
    
    ```bash
    $ git log
    ```
    
    - Mostrar abreviado:
        
        ```bash
        $ git log --oneline
        ```
        
    - Mostrar HEAD y master:
        
        ```bash
        $ git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
        ```
        
        - Si queremos además, ver los cambios en todas las ramas:
            
            ```bash
            $ git alias_que_hayamos_puesto --all
            ```
            
- Crear alias para evitar largos comandos:
    
    ```bash
    git config --global alias.el_nombre_que_quieras "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
    ```
    
- Recuperar versiones anteriores:
    
    ```bash
    $ git checkout identificador_del_commit
    ```
    
- Etiquetar versiones:
    
    ```bash
    $ git tag nombre_de_la_etiqueta
    ```
    
    - Si quieres ver todas la etiquetas:
        
        ```bash
        $ git tag
        ```
        
    - Si quieres borrar alguna etiquetas:
        
        ```bash
        $ git tag -d nombre_etiqueta
        ```
        
- Para comparar versiones:
    
    ```bash
    $ git diff nombre_version1 nombre_version2
    ```
    
- Para deshacer cambios antes del add:
    
    ```bash
    $ git restore nombre_archivo
    ```
    
    - Para deshacer antes del commit:
        
        ```bash
        $ git restore --staged nombre_archivo
        ```
        
- Deshacer el último commit:
    
    ```bash
    $ git revert HEAD --no-edit
    ```
    
- Borrar commits de una rama:
    
    ```bash
    $ git reset --hard version_commit
    ```
    
- Mover archivos:
    
    ```bash
    $ git mv nombre_archivo lugar_a_mover
    ```
    
- Borrar archivos:
    
    ```bash
    $ git rm nombre-archivo
    ```
    

## Ramas

- Crear rama:
    
    ```bash
    $ git branch nombre_rama
    ```
    
- Cambiar de rama:
    
    ```bash
    $ git checkout nombre_rama
    ```
    
    - Crear y cambiar a una rama:
        
        ```bash
        $ git checkout nombre_rama
        ```
        
- Incorporar cambios de una rama a otra:
    
    ```bash
    $ git checkout nombre_rama_donde_quieres_los_cambios
    $ git merge nombre_rama_que_tiene_los_cambios
    ```
    
- Para mover el HEAD donde queramos (”revertir” commits):
    
    ```bash
    $ git reset --hard hash_del_commit
    ```
    
- Fusionar ramas dejando solo 1 rama:
    
    ```bash
    $ git checkout nombre_rama_que_tiene_los_cambios
    $ git rebase nombre_rama_donde_quieres_los_cambios
    ```
    

---

## Ignorando archivos globalmente

- Para crear un archivo global para Git y que ignore ciertos archivos al hacer el manejo en el repositorio:
    
    ```bash
    $ git config --global core.excludesfile $HOME/.gitignore_global
    ```
    
    Creamos el archivo `.gitignore_global` en la raíz y añadimos: 
    
    ```bash
    # Compiled source #
    ###################
    *.com
    *.class
    *.dll
    *.exe
    *.o
    *.so
    
    # Packages #
    ############
    # it's better to unpack these files and commit the raw source
    # git has its own built in compression methods
    *.7z
    *.dmg
    *.gz
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip
    
    # Logs and databases #
    ######################
    *.log
    *.sql
    *.sqlite
    
    # OS generated files #
    ######################
    .DS_Store
    .DS_Store?
    ._*
    .Spotlight-V100
    .Trashes
    ehthumbs.db
    Thumbs.db
    *~
    *.swp
    
    # IDEs               #
    ######################
    .idea
    .settings/
    .classpath
    .project
    ```