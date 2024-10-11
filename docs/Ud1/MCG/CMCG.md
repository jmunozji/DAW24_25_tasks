---
title: 'cheatsheet de git - mauricio'
---
# CheatSheet de Git - mauricio
## Setup inicial
Antes de poder utilizar git, necesitamos **git**. En la amplia mayoría de sistemas GNU/Linux usaremos este comando:

 ```
sudo apt-get install git
```

Por último, necesitaremos especificar un **nombre** y un **correo electrónico**.

```
git config --global user.name "nombre"
git config --global user.email abc@email.com
```

 
## Manejo de repos.
Para crear un repositorio local y consultar su estado usaremos:

```
git init
git status
```
Podemos añadir y borrar archivos o cambios al/del repo con: 

```
git add	
git rm
```
Antes de subir ningún cambio deberíamos primero revisar si hay cambios, y si hay, bajarlos si asi lo queremos.
```
git fetch
git pull
```
Para confirmar los cambios y subirlos:
```
git commit -m "comentario"
git push
```
> Con el argumento "-m" especificamos un comentario para el commit.

Si quisieramos descartar los cambios hechos a un archivo y/o del commit más reciente estas son las instrucciones para hacerlo:
```
git restore archivo.bin
git revert HEAD --no-edit
```
Podemos añadir etiquetas al estado actual usando:
```
git tag nombre
```
Si quisieramos ver las diferencias entre commits, archivos e incluso ramas podemos usar el siguiente comando:

```
git diff "a" "b"
```
>Por defecto este comando, compara los cambios del último commit con los actuales. Tambien podemos hacer uso de tags a la hora de comparar.
***
### Historial de cambios.
Para poder ver el historico de todas nuestras ediciones a lo largo del tiempo usaríamos el siguiente comando:

```
git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```
Como este comando es brutalmente largo, podemos facilitarnos la vida creando un alias:

 ```
git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
 ```

De esta forma solo hace falta que escribamos ***git hist*** para poder ver todos los cambios de forma bonita.
***
Si quisieramos volver a un estado anterior, basta con ejecutar la siguiente orden:

```
git checkout "hash"
```

> Mucho ojo, pues "hash" debe remplazarse con el código numerico correspondiente al commit que quisieramos restablecer. Se puede ver en el historico anterior.


Tambien cabe añadir que si no hacemos un `git switch -` para volver al commit más reciente o cambiamos de rama, podemos liarla pardísima. Y hablando de ramas.

## Manejo de ramas.

Para crear una nueva rama de desarrollo en nuestro repositorio y situarnos dentro de ella usaremos los siguientes comandos:

```
git branch nombreRama
git checkout nombreRama
```
Para fusionar los cambios de una rama a la otra usaremos: 
```
git merge MAIN
```

Tambien podemos hacer un *rebase*, que nos permite cambiar la base de los commits hechos en la rama.

```
git rebase MAIN
```
>Mucho ojo, pues con las dos instruciónes anteriores le decimos a git que queremos aplicar los cambios de la rama actual en la que estamos trabajando a la rama main.
> Repito, rama actual ----> MAIN.

## GitHub o repos en linea.
Para poder clonar un repositorio existente ya sea en Github o derivados podemos hacerlo con el siguiente comando.
```
git clone "url"
```

Si quisieramos sincronizar un repositorio local a uno en linea utilizariamos la siguiente instrucción:
```
git remote add origin "ssh"
```
***
Mientras trabajamos en local junto a un repo en linea, no es raro que a la hora de hacer commits haya problemas, especialmente porque hay que verificar que no estamos sobreescribiendo el trabajo de nadie.

Para guardar nuestro trabajo local temporalmente podemos hacer:
```
git stash save
```

Nos bajamos los últimos cambios con los comando anteriormente vistos y restauramos nuestra sesión con: 

```
git stash pop
```
