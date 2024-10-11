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

 
## Manejo básico de repos
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

## Manejo de ramas

Para crear una nueva rama de desarrollo en nuestro repositorio y situarnos dentro de ella usaremos los siguientes comandos:

```
git branch nombreRama
git checkout nombreRama
```
Para fusionar los cambios de una rama a la otra usaremos: 
```
git merge MAIN
```
>Mucho ojo, pues con la instrución anterir le decimos a git que queremos aplicar los cambios de la rama actual en la que estamos trabajando a la rama main.
> Repito, rama actual ----> MAIN.
