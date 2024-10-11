---
title: 'CheatSheet Darius Gabriel Dobre'
---
# CheatSheet Github DARIUS GABRIEL DOBRE

## 1.2 Configuración Inicial
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu.email@ejemplo.com"
```

## 1.5 Uso básico de Git
Inciar un repositorio git y clonar un repositorio

```bash
git init
git clone <url_del_repositorio>
```

### Trabajar con Cambios
Para consultar el estado de los archivos

```bash
git status
```

Para agregar archivos al **staging area**, si haces `git add . ` se añden todos los archivos sin necesidad de especificar cual quieres añadir.

```bash
git add <archivo>
```
Para pasar tus archivos a Local Repository

```bash
git commit -m "Mensaje de commit"
```

Realiar add y commit en un solo comando

```bash
git commit -am "Mensaje de commit"
```


### Consultar Logs

Para consultar los logs podemos ejecutar los siguientes comandos

```bash
git log
git log --oneline
git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```

Podemos crear un alias para no tener que usar comandos tan largos

```bash
git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
```

Ahora simplemente con usar `git hist` nos saldrán los logs de una manera más vistosa


### Regresar a versiones anteriores

Podemos regresar a versiones anterior usando el **checkout** junto al **hash** del commit

Ejemplo:

```bash
git checkout e19f2c1
```
Al volver a esa versión, si queremos estar de vuelta al commit mas reciente:

```bash
git switch -
```


### Etiquetar versiones

Podemos etiquetar versiones usando el comando `tag`

Ejemplo:

```bash
git tag v1
```


### Visualizar cambios

Podemos visualizar los cambios que se han hecho en un archivo usando el comando `diff`

Ejemplo:

```bash
git diff v1-beta v1
```

## 1.6 Uso avanzado de Git

### Deshacer cambios

Si queremos deshacer los cambios que se han hecho en el archivo `README.md` podemos usar el comando `restore`

Ejemplo:

```bash
git restore README.md
```

Deshacer cambios antes del commit.

Si hemos realizado el comando `git add <arhivo>` y queremos deshacer los cambios antes de realizar el commit, podemos hacerlo de la siguiente manera:

```bash
git restore --staged <archivo>
```

Si en cambio ya hemos realizado el commit y queremos deshacerlo, lo haremos de la siguiente manera:

```bash
git revert HEAD --no-edit
```

Borrar commits de una rama:

```bash
git reset --hard <commit_id>
```

## 1.7 Ramas

### Crear una rama

```bash
git branch hola
```

### Cambiar de rama

```bash
git checkout <rama>
```
o
```bash
git checkout -b <nueva_rama>
```

Con esta última, creamos la rama y nos cambiamos a ella

### Fusionar Ramas

Usaremos este comando cuando queramos fusionar una rama con otra, nos posicionaremos sobre la rama a la que queramos traerle los cambios de otra y ejecutamos el siguiente comando:

```bash
git merge <rama_a_fusionar>
```

Otra manera de fusionar es con el `rebase` que lo que hace el alterar el orden o la historia de creación de cambios

```bash
git rebase <rama_a_fusionar>
```

## 1.8 GitHub

Para añadirte un repositorio remoto a local, lo tendrias que hacer de la siguiente manera:

```bash
git remote add <nombre> <url>
```

Para subir los cambios al repositorio remoto:

```bash
git push <nombre> <rama>
```

Para actualizar los cambios del repositorio remoto al local:

```bash
git pull <nombre> <rama>
```

Si quieres ver solamente si ha habido algun cambio, sin pasarte los cambios puedes hacer lo siguiente:

```bash
git fetch <nombre>
```

Para poder hacer un pull aunque tengas cambios en local que aun no has subido, lo que puedes guardar tus archivos en una especie de pila con el siguiente comando:

```bash
git stash save
```
y luego hacer el pull

```bash
git pull
```
Y por último sacamos los cambios de la pila:

```bash
git stash pop
```

Podemos realizar todo a la vez con un solo comando:

```bash
git pull --autostash
```