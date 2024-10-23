---
title: 'CH1 - Bryan Oyola'
---

## ÍNDICE

- [ÍNDICE](#índice)
- [INSTALACIÓN DE GIT](#instalación-de-git)
  - [Windows](#windows)
  - [Linux](#linux)
- [CONFIGURACIÓN DE GIT CON GITHUB](#configuración-de-git-con-github)
  - [Crear un repositorio en GitHub](#crear-un-repositorio-en-github)
  - [Crear un gitignore de forma global](#crear-un-gitignore-de-forma-global)
- [RESUMEN DE COMANDOS PRINCIPALES DE GIT](#resumen-de-comandos-principales-de-git)
  - [Comandos básicos](#comandos-básicos)
  - [Comandos avanzados](#comandos-avanzados)
- [TRABAJANDO CON RAMAS](#trabajando-con-ramas)
- [GUARDANDO CAMBIOS EN STASH](#guardando-cambios-en-stash)

## INSTALACIÓN DE GIT

La instalación de Git variará dependiendo del sistema operativo en el cual 
busquemos instalarlo.

### Windows
La forma más sencilla de tener una consola de git utilizable desde Windows es instalando Git [desde esta página web.](https://git-scm.com/downloads/win)

![Git SCM](https://git-scm.com/images/logo@2x.png)

Luego de ello, simplemente nos quedará lanzar el programa git bash, teniendo una terminal de comandos para git completamente funcional además de comandos de Linux muy útiles como:

-`ls (listar archivos y carpetas)`
-`cd (Recorrer los directorios)`
-`mkdir (crear carpetas)`
-`touch (crear ficheros)`

### Linux

Para instalar git en Linux, haremos uso del gestor de paquetería que nuestro sistema operativo tenga instalado, para ello, lo desglosaré en las 3 principales distribuciones que tiene el ecosistema Linux.

1. **Basadas en Debian(Debian,Ubuntu,Linux Mint...)**

    - Actualizar paquetería
    ```bash
    $sudo apt-get update && sudo apt-get upgrade
    ```
    - Instalar Git
    ```bash
    $sudo apt-get install git
    ```

1. **Basadas en Red Hat(Red Hat Linux,Fedora,OpenSuse...)**

    **Fedora**

    - Actualizar paquetería
    ```bash
    $sudo dnf check-update && sudo dnf upgrade
    ```
    - Instalar Git
    ```bash
    $sudo dnf install git
    ```

    **OpenSuse**

    - Actualizar paquetería
    ```bash
    $sudo zypper refresh && sudo zypper update
    ```
    - Instalar Git
    ```bash
    $sudo zypper install git
    ```

1. **Basadas en Arch(Arch Linux,Manjaro,Arco Linux...)**

    - Actualizar paquetería
    ```bash
    $sudo pacman -Syu
    ```
    - Instalar Git
    ```bash
    $sudo pacman -S git
    ```

## CONFIGURACIÓN DE GIT CON GITHUB

Ahora que ya tenemos una terminal con git completamente funcional, pasaremos a añadir nuestra cuenta de github(Esto lo haremos una vez en cada ordenador que queramos usar).

Para ello, lo primero que haremos será generar una clave ssh en nuestro ordenador, esta clave la utilizaremos para acceder a github de forma segura sin necesidad de estar logeandonos todo el tiempo.

para ello, introduciremos este comando:

- Si utilizas un ordenador moderno(prótocolo más seguro)
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- Si utilizas un ordenador más antiguo
```bash
 ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Esto nos generará dos claves ssh (una pública que será la que usaremos y otra privada que debemos conservar y no mostrar a nadie) en la carpeta ./ssh en nuestro directorio `$HOME` (Carpeta de usuario de Linux). Algo parecido a lo que generamos cuando queremos conectarnos a un servidor AWS, sería casi lo mismo que el archivo .PEM pero para github.

Luego accederemos a nuestra cuenta de Github desde un navegador e iremos al apartado Settings->SSH and GPG Keys->New SSH Key.

- Mediante el comando:
```bash
$cat ~/.shh/{nombre-del-archivo}.pub 
```
Copiaremos la clave pública ssh y en la sección anterior, la pegaremos y le daremos un nombre para identificar que ordenador es.

Por último, debemos añadir nuestro nombre e email de github en la terminal para que así podamos ser identificados de forma correcta en los commit que hagamos, además de cambiar la rama predeterminada de master a main.

- Utilizaremos los comandos
```bash
$git config --global user.name "Nombre Cuenta Github"
$git config --global user.email "Correo de Github"
$git config --global init.defaultBranch main
```

Con esto, ya tendremos listo nuestro entorno de trabajo, que usaremos cuando ejecutemos comandos más avanzados y dejemos de trabajar en local.

### Crear un repositorio en GitHub

1. Inicias sesión en [GitHub](https://github.com).
1. Pulsas sobre el icono y luego en repositorios.
1. Creas un nuevo repositorio pulsando en New.
1. Decides un nombre para el repositorio.
1. Puedes ponerle una descripción.
1. Eliges si quieres que sea público o privado.
1. Puedes añadir un README, un gitignore o una Licencia.
1. Por último, ya puedes crear el repositorio.

### Crear un gitignore de forma global

Un gitignore es un archivo muy útil para no llenar nuestros repositorios de git de archivos innecesarios. 

Muchos frameworks o librerías crean archivos residuales que seguramente no busquemos mantener en nuestro repositorio final del proyecto, para ello, crearemos un gitignore. 

Podemos crear uno por cada proyecto que tengamos simplemente creandolo en la carpeta raíz del directorio git o podemos crear uno global si usamos muy a menudo ciertas tecnologías.

Para ello, utilizaremos:
```bash
$git config --global core.excludesfile $HOME/.gitignore_global
```

## RESUMEN DE COMANDOS PRINCIPALES DE GIT

### Comandos básicos

- `git init`: Inicializa un nuevo repositorio.
```bash
$mkdir pruebas
$cd pruebas
$git init

Inicializado repositorio Git vacío en /home/bryan/Documentos/DAW/pruebas/.git/
```
- `git status`: Consulta el estado de un proyecto.
```bash
$git status

En la rama main
Tu rama está actualizada con 'origin/main'.

nada para hacer commit, el árbol de trabajo está limpio
```
- `git log`: Consulta el historial de commits en el repositorio actual.
```bash
$git log
$git log --oneline
$git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```
- `git tag`: Crea,modifica o elimina "tags"(etiquetas) a los commits(instantáneas) de nuestro proyecto.
```bash
$git tag
<Lista todas las etiquetas creadas>
$git tag <nombre>
<Crea una etiqueta llama nombre>
$git tag -d <nombre>
<Elimina la etiqueta llamada nombre>
```
- `git add <nombre archivo>`: Añade los cambios al área de preparación ->(staging).
```bash
$touch archivo.txt
$git add archivo.txt <Esto añadiría archivo.txt al área de staging>

$git add . <Esto añadiría todos los ficheros al área de staging>
```
- `git commit -m`: Crea una instantánea de los cambios que hay en stage.
```bash
$git commit -m "commit inicial"
<Si le quitamos la -m, nos abrirá un editor de texto para añadir el mensaje>
$git commit -am "commit inicial"
<Nos hará el git add y el git commit a la vez>
```
- `git diff`: Permite ver las diferencias entre distintos estados de tu repositorio, como entre commits, ramas o el estado actual de los archivos.
```bash
$git diff
<Muestra la diferencia entre tu área de trabajo y el commit más reciente>
$git diff <commit>
<Muestra la diferencia entre tu área de trabajo y el commit especificado>
$git diff <otra rama>
<Muestra la diferencia entre tu rama actual y otra rama>
```
- `git clone <url>`: Clona un repositorio existente.
```bash
$git clone <url_repositorio>
<La url puede ser la ssh o la http del repositorio, dependerá de nuestra configuración anterior, aunque es preferible la ssh>
```

### Comandos avanzados
- `git push`: Se utiliza para enviar tus cambios locales a un repositorio remoto.
```bash
$git push origin main
<git push <remoto> <rama>>
```
- `git fetch <url>`: Actualizar tu repositorio local con los cambios que han ocurrido en el repositorio remoto, este comando solo descarga la información sin mezclarla con tu trabajo local.
```bash
$git fetch origin
```
- `git merge`: Combina una rama con la actual, tambien, combina un fetch con la rama en la que estás trabajando.
```bash
$git merge <rama>
```
- `git pull`: Actualizar tu rama local con los cambios que se han hecho en el repositorio remoto. Combina dos acciones: git fetch (descargar los cambios) y git merge (añadir esos cambios en tu rama actual).
```bash
$git pull origin main
<git pull <remoto> <rama>>
```
- `git rebase <rama>`: Aplicar cambios de una rama sobre otra sobreescribiendo el historial de las ramas.
```bash
$git rebase <rama>
$git rebase --continue
```
- `git reset --hard <commit-hash>`: Resetea el repositorio a un commit específico.
```bash
$git reset <commit>
$git reset --hard <commit>
<Resetea el repositorio a un commit específico>
```

## TRABAJANDO CON RAMAS

**Listar Ramas**

```bash
$git branch
<Muestra las ramas que tienes en tu directorio local>
$git branch -r
<Muestra las ramas remotas que tienes>
```

**Creación y eliminación de Ramas**

```bash
$git branch <rama>
<Crear rama>
$git branch -d <rama>
<Eliminar rama>
```

**Trabajando con Ramas**

```bash
$git checkout <rama>
<Ir a la rama especificada>
$git checkout -b <rama>
<Creas la rama especificada y luego cambias a ella>
```

**Combinando Ramas**

```bash
$git merge <rama>
<Combina una rama con la actual>
$git rebase <rama>
<Aplicar cambios de una rama sobre otra sobreescribiendo el historial de las ramas>
```

- Se puede usar la opción -m para dar un nombre al commit al hacer el merge
- existen opciones como --ff -> fast-forward (Que intentará no crear un commit nuevo y por lo tanto no dejar historial de ramas) y --no-ff(Que creará un commit nuevo para la fusión).

## GUARDANDO CAMBIOS EN STASH

Muy útil si quieres mantener un trabajo actual y así poder hacer otra tarea diferente y luego de hacer lo anterior, volver a tu trabajo guardado en el stash.

```bash
$git stash
<Guardas los cambios en el stash>
$git pull, $git checkout <otra rama>
<Empiezas con tu nueva tarea hasta finalizarla mediante un commit por ejemplo>
$git stash pop
<Recuperas los cambios que tienes en el stash y prosigues con tu trabajo anterior>
```

- `git pull --autostash`: Es la combinación del primer git stash y git pull, en otras palabras, guarda tu trabajo en el stash y recibe los cambios del pull.
