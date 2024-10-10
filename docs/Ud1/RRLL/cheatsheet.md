# Cheatsheet GIT

## Configurar GIT

### Configurar nombre

```bash
git config --global user.name "nombre"
```

### Configurar email

```bash
git config --global user.email "email"
```

## Uso básico de git

### Inicializar un repositorio Git

```bash
git init
```

### Comandos básicos de Git

#### Consultar el estado de un proyecto

```bash
git status
```

#### Añadir el archivo o sus cambios al área de preparación (staging area)

```bash
git add nombre_del_archivo
```

#### Añadir los archivos de forma recursiva al área de preparación (staging area)

```bash
git add .
```

#### Confirmar los cambios

```bash
git commit -m "mensaje del commit"
```

#### Git ignore global

Primero debes configurar el archivo .gitignore_global, una vez lo tenegas dependiendo de donde esté ubicado el archivo, **$HOME** puede ser sustituido.

##### Aplicar el archivo .gitignore_global

```bash
git config --global core.excludesfile $HOME/.gitignore_global
```

#### Historial de los commits

```bash
git log
```

#### Crear un alias

```bash
git config --global alias.nombre_del_alias "Orden que realiza"
```

#### Volver a un commit anterior

```bash
git checkout hash_commit
```

##### Guardar los cambios en una nueva rama

Antes hay que realizar un **git add** y un **git commit** como hemos visto previamente

```bash
git switch -c nombre_rama
```

```bash
git checkout -b nueva_rama
```

##### Volver a la rama anterior

```bash
git switch -
```

```bash
git checkout master
```

#### Volver el estado del proyecto al commit deseado

```bash
git reset hash_commit
```

##### Variaciones

###### Soft

Al aplicar esta opcion los cambios de los commits se mantienen en la área de preparación (staging area) y los cambios realizados en el working directory siguen intactos.

```bash
git reset --soft hash_commit
```

###### Mixed

Al aplicar esta opcion los cambios de los commits se quitan del área de preparación (staging area) y los cambios realizados en el working directory siguen intactos, **es el que se aplica por defecto**.

```bash
git reset --mixed hash_commit
```

###### Hard

Al aplicar esta opcion los cambios de los commits se quitan del área de preparación (staging area) y los cambios realizados en el working directory también se pierden.

```bash
git reset --hard hash_commit
```

#### Etiquetas

##### Añadir etiqueta

```bash
git tag nombre_etiqueta
```

##### Ver etiquetas existentes

```bash
git tag
```

##### Borrar etiqueta

```bash
git tag -d nombre_etiqueta
```

#### Visualizar cambios

##### Cambios entre el último commit y el área de trabajo (working directory)

```bash
git diff
```

##### Cambios entre un commit y el work direcotry

```bash
git diff hash_commit
```

##### Cambios entre dos commits

```bash
git diff hash_commit has_commit
```

## Uso avanzado de Git

### Desacer cambios

#### Deshaciendo cambios antes de la fase de staging

```bash
git restore nombre_archivo
```

#### Deshaciendo cambios antes del commit

```bash
git restore --staged nombre_archivo
```

```bash
git restore nombre_archivo
```

#### Deshacer commits

```bash
git revert hash_commit --no-edit
```

Si quieres revertir al último comit donde head está apuntando

```bash
git revert HEAD --no-edit
```

#### Modificar un commit

```bash
git commit --amend -m "mensaje del commit"
```

### Renombrar y mover archivos

#### Renombrar

```bash
git mv nombre_archivo nuevo_nombre
```

#### Mover

```bash
git mv nombre_archivo carpeta
```

## Ramas

### Crear una nueva rama

```bash
git branch nombre_rama
```

Si queremos crear la rama y situarnos en ella directamente podemos usar cualquiera de estos dos comandos.

```bash
git switch -c nombre_rama
```

```bash
git checkout -b nombre_rama
```

### Ver nuestras ramas

```bash
git branch
```

### Cambiar de ramas

```bash
git switch nombre_rama
```

```bash
git checkout nombre_rama
```

### Fusión de ramas

```bash
git merge nombre_rama
```

```bash
git rebase nombre_rama
```

Los dos comandos sirven para fusionar ramas, la principal diferencia es que **merge** mantiene el historial completo de ambas ramas y se añade un nuevo commit que representa la fusión y rebase convierte el historial en una única linea sin bifurcaciones, no crea un commit de fusión.

### fast-forward y Squash

```bash
git merge --no-ff nombre_rama 
```

Por defecto es fast-forward, esto lo que signifca es que no deja rastro de la fusión, por ello es recomendable usar el parametro **--no-ff** para que quede constancia de que una rama se ha fusionado con otra.

```bash
git merge --squash nombre_rama 
```

Básicamente lo que hace es combinar todos los commits de la rama fusionada en un solo commit. Después de eso tenemos que hacer un commit manualmente.

![Imágen de ff y squash](https://github-1253518569.cos.ap-shanghai.myqcloud.com/2018-09-18_200521.png)

## GitHub

### Crear clave Pública/Privada

```bash
ssh-keygen -t rsa -C "Cuenta GitHub"
```

### Añadir SSH a GitHub

Copias el contenido de tu clave pública y lo añades en GitHub.

```bash
cat ~/.ssh/id_rsa.pub
```

### Crear el repositorio remoto

Previamente tienes que tener creado el repositorio en GitHub.

```bash
git remote add origin enlace_repositorio_GitHub
```

Si queires modificar la URL usa este comando

```bash
git remote set-url origin enlace_repositorio_GitHub
```

### Ver la configuración de nuestro repositorio remoto

```bash
git remote show origin
```

### Clonar un repositorio de GitHub

```bash
git clone enlace_repositorio_GitHub
```

### Subir archivos a GitHub

```bash
git push origin master
```

Si usas el comando de abajo gracias al parametro **-u** vinculamos nuestra rama local con una rama remota, de tal manera que no tengamos que estar siempre indicándolo cuando hagamos un pull o push

```bash
git push -u origin master
```

### Actualizar nuestro repositorio local

Hay dos formas de realizarlo.

#### Fetch y merge/rebase

```bash
git fetch
```

```bash
git merge origin/master
```

#### Pull

```bash
git pull 
```

Por defecto **git pull** lo que hace es un **git merge**, si queremos hacer **git rebase** deberemos especificarlos con el parámetro **-r** o **--rebase**.

```bash
git pull --rebase 
```

### Guardar los cambios no confirmados temporalmente

Esto generalmente es usado para solucionar conflictos que surgen cuando queremos hacer un **git pull** y no nos deja debido a que tenemos archivos son confirmar en local.

* Guardar los archivos sin confirmar.

```bash
git stash save
```

* Hacemos un push.

```bash
git stash save
```

* Recuperamos los archivos sin confirmar.

```bash
git stash pop
```

Git te permite hacer esto con un solo comando.

```bash
git pull --autostash
```

Y si quieres que además te haga un rebase, le añadimos lo que hemos visto anteriormente.

```bash
git pull --autostash --rebase
```

### Ramas en GitHub

#### Subir una nueva rama a GitHub

* Creamos la rama en local

```bash
git branch nombre_rama
```

* Hacemos un push de la rama

```bash
git push origin nombre_rama
```

#### Borrar una rama en GitHub

```bash
git push origin -d nombre_rama
```
