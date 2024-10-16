---
title: 'Ud1 Control de Versiones'
---

# **1 Instalación y configuración de Git**

## **1.1 Instalación de Git**

Para instalar Git en tu sistema operativo, sigue los siguientes pasos:

1. *Primero tienes que actualizar tu sistema operativo :*

```bash
    $ sudo apt-get update && upgrade
```

2. *Instalación en Linux :*

```bash
    $ apt-get install git
```

3. *Comprueba la versión de git que estás usando :*

```bash
    $ git --version
```

## **1.2 Configuración de Git**

Debes establecer tu nombre de usuario y dirección de correo electrónico. Esto es importante porque las confirmaciones de cambios _`(commits)`_ en Git usan esta información.

```bash
    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com
    $ git config --global push.default simple
```

## **1.3 Configuración de la clave SSH**



# **2 Uso básico de Git**

## **2.1 Estados de un archivo en GIT**

Un archivo puede estar en alguno de los siguientes estados:

* Sin seguimiento (untracked)
* Modificado (modified)
* Preparado (staged)
* Confirmado (commited)

Para consultar el estado de los archivos usamos el comando:

```bash
    $ git status
```

## **2.2 Crear un proyecto**

1. *Creamos un directorio donde colocar el código:*

```bash
    $ mkdir curso-de-git
    $ cd curso-de-git
```

2. *Creamos un fichero _`hola.php`_ que muestre Hola Mundo :*

```php
<?php
echo "Hola Mundo\n";
?>
```

3. *Crear el repositorio :*

```bash
    $ git init
    Initialized empty Git repository in /home/admin/curso-de-git/.git/
```

El archivo _`hola.php`_ estará en el Workspace o Working Directory.

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
        |                |                | 
    hola.php             |                | 
        |                |                | 
        +                +                +
```

4. *Añadir la aplicación :*

```bash
    $ git add hola.php
    $ git add . (Para añadir todos )
```

5. *Comprobar el estado del proyecto :*

```bash
    $ git status
```

Al ejecutar el _`git add`_, el archivo pasará a la "Staging area" o área de preparación.

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
        |                |                | 
        |             hola.php            |
        |                |                | 
        +                +                +
```

6. *Hacer un commit :*

```bash
    $ git commit -m "Creación del proyecto"
```

El archivo pasará al "Local Ropository" y se le asignará un _`hash`_ o código de inserción.

```
+-------------+  +-------------+  +-------------+  
|  Workspace  |  |   Staging   |  |    Local    | 
|             |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
        |                |                | 
        |                |             hola.php (e19f2c1)
        |                |                | 
        +                +                +
```

### **2.2.1 Ignorando archivos**

Para indicarle a git que debe ignorar un archivo, se puede crear un fichero llamado _`.gitignore`_, bien en la raíz del proyecto o en los subdirectorios que queramos.

Dicho fichero puede contener patrones, uno en cada línea, que especiquen qué archivos deben ignorarse. El formato es el siguiente:

```bash
    # .gitignore
    dir1/           # ignora todo lo que contenga el directorio dir1
    !dir1/info.txt  # El operador ! excluye del ignore a dir1/info.txt (sí se guardaría)
    dir2/*.txt      # ignora todos los archivos txt que hay en el directorio dir2
    dir3/**/*.txt   # ignora todos los archivos txt que hay en el dir3 y sus subdirectorios
    *.o             # ignora todos los archivos con extensión .o en todos los directorios
```

Los archivos que hemos metido en _`.gitignore`_, deben ser aquellos ficheros temporales o de configuración que se pueden crear durante las fases de compilación o ejecución del programa

### **2.2.2 Ignorando archivos globalmente**

Ejemplos de lo que se puede ignorar de forma global son los ficheros temporales del sistema operativo _`(*~, .nfs*)`_ y los que generan los entornos de desarrollo.

Para indicar a git que queremos tener un fichero de gitignore global, tenemos que configurarlo con la siguiente orden:

```bash
    $ git config --global core.excludesfile $HOME/.gitignore_global
```

Ahora podemos crear un archivo llamado _`.gitignore_global`_ en la raíz de nuestra cuenta con este contenido:

```
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

## **2.3 Trabajando con el historial**

### **2.3.1 Observando los cambios**

Con la orden _`$git log`_ podemos ver todos los cambios que hemos hecho al directorio _`curso-de-git`_.

```bash
    $ git log
    commit fd4da946326fbe8b24e89282ad25a71721bf40f6  (HEAD -> master)
    Author: Sergio Gómez <sergio@uco.es>
    Date:   Sun Jun 16 12:51:01 2013 +0200

        Se añade un comentario al cambio del valor por defecto

    commit 3283e0d306c8d42d55ffcb64e456f10510df8177
    Author: Sergio Gómez <sergio@uco.es>
    Date:   Sun Jun 16 12:50:00 2013 +0200

        Se añade un parámetro por defecto

    commit efc252e11939351505a426a6e1aa5bb7dc1dd7c0
    Author: Sergio Gómez <sergio@uco.es>
    Date:   Sun Jun 16 12:13:26 2013 +0200

        Parametrización del programa

    commit e19f2c1701069d9d1159e9ee21acaa1bbc47d264
    Author: Sergio Gómez <sergio@uco.es>
    Date:   Sun Jun 16 11:55:23 2013 +0200

        Creación del proyecto
```

Para salir escribe _`q`_.

### **2.3.2 Versiones abreviadas**

```bash
    $ git log --oneline
    $ git log --oneline --max-count=2
    $ git log --oneline --since='5 minutes ago'
    $ git log --oneline --until='5 minutes ago'
    $ git log --oneline --author=sergio   # Cambia sergio por tu nombre de usuario
    $ git log --oneline --all
    $ git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```

### **2.3.3 Crear alias**

Como estas órdenes son demasiado largas, Git nos permite crear alias para crear nuevas órdenes parametrizadas. 

1. *Para ello podemos configurar nuestro entorno con la orden `git config` de la siguiente manera :*

```bash
    $ git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
```

2. *Ahora basta con ejecutar :*

```bash
    $ git hist
```

### **2.3.4 Recuperando versiones anteriores**

1. *Regresar a un commit anterior :*

Cada cambio es etiquetado por un _`hash`_, para poder regresar a ese momento del estado del proyecto se usa la orden :

```bash
    $ git checkout 
```

Prueba con el _`hash`_ de tu primer commit:

```bash
    $ git checkout e19f2c1
    Note: switching to 'e19f2c1'.

    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.

    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:

    git switch -c <new-branch-name>

    Or undo this operation with:

    git switch -

    urn off this advice by setting config variable advice.detachedHead to false

    HEAD is now at e19f2c1 Parametrización del programa   
```

Esto pone el repositorio en un estado _`"detached HEAD"`_, donde puedes explorar o hacer cambios sin afectar ninguna rama.

Los cambios que realices en este estado no se guardarán a menos que los comites a una nueva rama:

```bash
    $ git switch -c <nombre-nueva-rama>
```

### **2.3.5 Volver a la última rama**

Si decides no mantener los cambios hechos en el estado _`"detached HEAD"`_.

Puedes volver a la rama en la que estabas antes con:

```bash
    $ git switch -
```

Los cambios no comiteados en el estado _`"detached HEAD"`_ se perderán.

### **2.3.6 Volver a la rama master**

Para regresar a la rama principal _`(master o main)`_ usa:

```bash
    $ git checkout master
```

### **2.3.7 Etiquetar versiones**

Listar todas las etiquetas:

```bash
    $ git tag
```

Para facilitar la navegación y recuperación de versiones sin usar hashes, puedes etiquetar un commit con un nombre amigable:

```bash
    $ git tag <nombre-tag>
```

*Ejemplo:*

```bash
    $ git tag v1
```

Para etiquetar un commit anterior, puedes usar:

```bash
    $ git checkout v1^
    $ git tag v1-beta
```

### **2.3.8 Ver cambios entre versiones**

Puedes comparar los cambios entre dos etiquetas o commits con:

```bash
    $ git diff <version-antigua> <version-nueva>
```

*Ejemplo:*

```bash
    $ git diff v1-beta v1
```

### **2.3.9 Borrar etiquetas**

Si necesitas eliminar una etiqueta:

```bash
    $ git tag -d <nombre-etiqueta>
```

### **2.3.10 Visualizar el historial con etiquetas**

Para ver el historial del proyecto junto con las etiquetas:

```bash
    $ git hist master --all
```

# **3 Uso avanzado de Git**

## **3.1  Deshacer cambios**

### **3.1.1 Deshaciendo cambios antes de la fase de staging**

1. *Volvemos a la rama máster y vamos a modificar el comentario que pusimos:*

```bash
    $ git checkout master
    Previous HEAD position was 3283e0d... Se añade un parámetro por defecto
    Switched to branch 'master'
```

Recordamos, la situación es la siguiente:

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
       |                |                |
       |                |             hola.php (fd4da94) tag: v1 
       |                |             hola.php (3283e0d) tag: v1-beta  
       |                |             hola.php (efc252e)
       |                |             hola.php (e19f2c1) 
       |                |                | 
       +                +                +
```

2. *Modificamos _`hola.php`_ de la siguiente manera :*

```php
<?php
// Este comentario está mal y hay que borrarlo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
?>
```

3. *Comprobamos el estado :*

```bash
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    
       modified:   hola.php
    
    no changes added to commit (use "git add" and/or "git commit -a")
```

Tenemos hola.php en Working Directory y nada en Staging Area.

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
       |                |                |
    hola.php            |                |
       |                |             hola.php (fd4da94) tag: v1 
       |                |             hola.php (3283e0d) tag: v1-beta  
       |                |             hola.php (efc252e)
       |                |             hola.php (e19f2c1) 
       |                |                | 
       +                +                +
```

4. *El mismo Git nos indica que debemos hacer para añadir los cambios o para deshacerlos.* 

En este caso los desharemos :

```bash
    $ git restore hola.php
    
    $ git status
    On branch master
    nothing to commit, working tree clean
    
    $ cat hola.php
    <?php
    // El nombre por defecto es Mundo
    $nombre = isset($argv[1]) ? $argv[1] : "Mundo";
    @print "Hola, {$nombre}\n";
    ?>
```

### **3.1.2 Deshaciendo cambios antes del commit**

Vamos a hacer lo mismo que la vez anterior, pero esta vez sí añadiremos el cambio al _`staging`_ **(sin hacer _`commit`_)**.

1. *Volvemos a modificar _`hola.php`_ igual que la anterior ocasión :*

```php
<?php
// Este comentario está mal y hay que borrarlo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
?>
```

2. *Lo añadimos al _`staging`_ :*

```bash
    $ git add hola.php

    $ git status
    On branch master
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
    
       modified:   hola.php
```
Ahora tenemos una nueva versión de _`hola.php`_ en Staging Area.

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
       |                |                |
       |             hola.php            |
       |                |             hola.php (fd4da94) tag: v1 
       |                |             hola.php (3283e0d) tag: v1-beta  
       |                |             hola.php (efc252e)
       |                |             hola.php (e19f2c1) 
       |                |                | 
       +                +                +
```

3. *Sacamos el archivo de la zona de Staging :*

```bash
    $ git restore --staged hola.php
    
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    
       modified:   hola.php
    
    no changes added to commit (use "git add" and/or "git commit -a")
```

Vuelve a estar en Working Directory.

```
+-------------+  +-------------+  +-------------+  
|  Working    |  |   Staging   |  |    Local    | 
|  Directory  |  |     Area    |  |  Repository | 
+------+------+  +------+------+  +------+------+ 
       |                |                |
    hola.php            |                |
       |                |             hola.php (fd4da94) tag: v1 
       |                |             hola.php (3283e0d) tag: v1-beta  
       |                |             hola.php (efc252e)
       |                |             hola.php (e19f2c1) 
       |                |                | 
       +                +                +
```

4. *Restaruramos la última versión en Local Repository, eliminando la versión en Working Directory :*

```bash
  $ git restore hola.php
```

Y ya tenemos nuestro repositorio limpio otra vez.

Como vemos hay que hacerlo en dos pasos:

* Uno para pasar el fichero de Staging Area a Working Directory y limpiar así la Staging Area.
* Otro para descartar los cambios en Working Directory.

### **3.1.3 Deshaciendo commits no deseados**

Si hemos hecho un commit y queremos deshacerlo, podemos usar la orden _`$ git revert`_ :

```bash
    $ git revert HEAD --no-edit
    [master 817407b] Revert "Ups... este commit está mal"
    1 file changed, 1 insertion(+), 1 deletion(-)
```
Explicación del comando:

* git revert HEAD: Reviertes el último commit (el que apunta HEAD). Esto crea un nuevo commit que deshace los cambios realizados en ese commit.
* --no-edit: Este parámetro le indica a Git que use el mensaje de commit por defecto que genera automáticamente (algo como "Revert 'mensaje original del commit'") y no abra el editor para modificarlo.

```bash    
    $ git hist
    * 817407b 2013-06-16 | Revert "Ups... este commit está mal" (HEAD -> master) [Sergio Gómez]
    * 5a5d067 2013-06-16 | Ups... este commit está mal [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```
### **3.1.4 Borrar commits de una rama**

Para borrar commits y no dejar huella en el historial, utilizamos _`$ git reset`_ :

```bash
    $ git reset --hard <nombre-o-hash-del-commit>
    $ git reset --hard v1
    HEAD is now at fd4da94 Se añade un comentario al cambio del valor por defecto
    
    $ git hist
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (HEAD -> master, tag: v1) [Sergio Góme
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```
!!! danger

    La orden _reset_ es una operación delicada. Debe evitarse si no se sabe bien lo que se está haciendo,
    sobre todo cuando se trabaja en repositorios compartidos, porque podríamos alterar la historia de cambios
    lo cual puede provocar problemas de sincronización.

### **3.1.5 Modificar un commit**

Si olvidaste añadir un cambio a un commit reciente, puedes modificar el último commit.

1. *Volvemos a modificar nuestro archivo :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
// El nombre por defecto es Mundo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
?>
```

2. *Usamos `commit --amend` que nos permite modificar el último estado confirmado, sustituyéndolo por el estado actual :*

```bash
    $ git add hola.php
    
    $ git commit --amend -m "Añadido el autor del programa y su email"
    [master 96a39df] Añadido el autor del programa y su email
     1 file changed, 1 insertion(+)
    
    $ git hist
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email (HEAD -> master) [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

!!! danger

    Nunca modifiques un _commit_ que ya hayas sincronizado con otro repositorio o que hayas recibido de él. Estarías alterando la historia de cambios y provocarías problemas de sincronización.

## **3.2  Moviendo y borrando archivos**

### **3.2.1 Mover un archivo a otro directorio con git**

Para mover archivos usaremos la orden _`$ git mv`_:

```bash
    $ mkdir lib
    
    $ git mv hola.php lib
    
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
      renamed:    hola.php -> lib/hola.php
```    

### **3.2.2 Borrar archivos**

1. *Borrar un archivo :*

```bash
    $ git rm hola.php
```

2. *Guardar los cambios :*

```bash
    $ git commit -m "Movido hola.php a lib."
    [master 8c2a509] Movido hola.php a lib.
     1 file changed, 0 insertions(+), 0 deletions(-)
     rename hola.php => lib/hola.php (100%)
```

# **4 Ramas**

## **4.1 Administración de ramas**

### **4.1.1 Crear una nueva rama**

Es conveniente trabajar en una nueva rama, para no modificar la rama principal y dejarla inestable. 

Aunque la orden para manejar ramas es _`$ git branch`_ podemos usar también _`$ git checkout`_.

1. *Crear una nueva rama :*

```bash
    $ git branch hola
```
!!! info

    Si usamos `git branch` sin ningún argumento, nos devolverá la lista de ramas
    disponibles.

2. *La creación de una rama no cambia automáticamente a esa rama, por lo que debemos cambiar con :*

```bash
  $ git checkout hola
    Switched to branch 'hola'
```

!!! tip

    Puedes hacer ambas acciones en un solo paso : 
        * Con el parámetro `-b` de `git checkout` podemos cambiarnos a una rama que, si no existe, se crea instantáneamente.
    
            $ git checkout -b hola
            Switched to a new branch 'hola'
        

### **4.1.2 Modificaciones en la rama secundaria**

1. *Añadimos un nuevo archivo en el directorio `lib` llamado `HolaMundo.php` :*

```php
<?php

class HolaMundo
{
   private $nombre;

   function __construct($nombre)
   {
      $this->nombre = $nombre;
   }

   function __toString()
   {
      return sprintf ("Hola, %s.\n", $this->nombre);
   }
}
?>
```

2. *Modificamos `hola.php` :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
// El nombre por defecto es Mundo
require('HolaMundo.php');

$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
print new HolaMundo($nombre);
?>
```

3. *Hacemos commit de los cambios uno por uno :*

```bash
    $ git add lib/HolaMundo.php
    $ git commit -m "Añadida la clase HolaMundo"

    $ git add lib/hola.php
    $ git commit -m "hola usa la clase HolaMundo"
```

4. *Y con la orden `git checkout` podemos movernos entre ramas :*

```bash
    $ git checkout master
    Switched to branch 'master'
    
    $ git checkout hola
    Switched to branch 'hola'
```

### **4.1.3 Modificaciones en la rama master**

1. *Cambiamos a la rama `master` :*

```bash
    $ git checkout master
```

2. *Añadimos un archivo `README.md` :*

```bash
    $ echo "# Curso de GIT" > README.md
    $ git add README.md
    $ git commit -m "Añadido README.md"
```

3. *Podemos visualizar todos los cambios en todas las ramas usando :*

```bash
    $ git hist --all
```

## **4.2 Fusión de ramas y resolución de conflictos**

### **4.2.1 Mezclar ramas**

Podemos fusionar los cambios de una rama con otra usando _`$git merge`_.

Por ejemplo, fusionamos los cambios de _`master`_ en la rama _`hola`_ :

1. *Cambiamos a la rama `hola` :*

```bash
  $ git checkout hola
    Switched to branch 'hola'
```

2. *Ejecutamos el `$git merge nombrerama` con el nombre de la rama cuyos cambios queremos incorporar a la actual :*

```bash
 $ git merge master
    Merge made by the 'ort' strategy.
     README.md | 3 +++
     1 file changed, 3 insertions(+)
     create mode 100644 README.md
```

3. *Comprobamos el historial de cambios :*

```bash
 $ git hist --all
    *   9c6ac06 2013-06-16 | Merge branch 'master' into hola (HEAD -> hola) [Sergio Gómez]
    |\
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * | 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
    * | 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

De esa forma se puede trabajar en una rama secundaria incorporando los cambios de la rama principal o de otra rama.

### **4.2.2 Resolver conflictos**

Un conflicto es cuando se produce una fusión que Git no es capaz de resolver.

*Vamos a modificar la rama `master` para crear uno con la rama `hola`:*

1. *Cambiamos a `master` :*

```bash
 $ git checkout master
    Switched to branch 'master'
```

2. *Modificamos nuestro archivo `hola.php` :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
print "Introduce tu nombre:";
$nombre = trim(fgets(STDIN));
@print "Hola, {$nombre}\n";
?>
```

3. *Guardamos los cambios :*

```bash
    $ git add lib/hola.php
    
    $ git commit -m "Programa interactivo"
    [master 9c85275] Programa interactivo
     1 file changed, 2 insertions(+), 2 deletions(-)
    
    $ git hist --all
```

4. *Comprobamos el resultado :*

```bash
 $ git hist --all
    * 8ca56f7 2013-06-16 | Programa interactivo (HEAD -> master) [Sergio Gómez]
    | *   9c6ac06 2013-06-16 | Merge branch 'master' into hola (hola) [Sergio Gómez]
    | |\  
    | |/  
    |/| 
    * | c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    | * 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
    | * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

5. *Volvemos a la rama `hola` :* 

```bash
  $ git checkout hola
    Switched to branch 'hola'
```

6. *Fusionamos.* 

```bash
  $ git merge master
    Auto-merging lib/hola.php
    CONFLICT (content): Merge conflict in lib/hola.php
    Automatic merge failed; fix conflicts and then commit the result.
```

Recuerda que hemos modificado hola.php en la rama `master` y ahora hay líneas distintas dentro de ese fichero en cada una de las ramas.

7.  *Editamos nuestro archivo `lib/hola.php` y obtendremos algo similar a esto :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
<<<<<<< HEAD
// El nombre por defecto es Mundo
require('HolaMundo.php');

$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
print new HolaMundo($nombre);
=======
print "Introduce tu nombre:";
$nombre = trim(fgets(STDIN));
@print "Hola, {$nombre}\n";
>>>>>>> master
?>
```

8. *Resolvemos el conflicto, dejando el archivo como sigue :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
require('HolaMundo.php');

print "Introduce tu nombre:";
$nombre = trim(fgets(STDIN));
print new HolaMundo($nombre);
?>
```

9. *Confirmamos los cambios :*

```bash
    $ git add lib/hola.php
    $ git commit -m "Solucionado el conflicto al fusionar con la rama master"
    [hola a36af04] Solucionado el conflicto al fusionar con la rama master
```

### **4.2.3 Rebasing vs Merging**

#### **4.2.3.1 Rebasing**

Rebasing es otra técnica para fusionar distinta a merge y usa la orden _`$git rebase`_ : 

    El rebase Vuelve a aplicar todos los cambios a la rama máster, desde su nodo más reciente.
    Eso significa que se modifica el orden o la historia de creación de los cambios. 
    Por eso rebase no debe usarse si el orden es importante o si la rama es compartida.

1. *Vamos a dejar nuestro proyecto como estaba antes del fusionado.*

```bash
    $ git checkout hola
    Switched to branch 'hola'

    $ git hist
    *   a36af04 2013-06-16 | Solucionado el conflicto al fusionar con la rama master (HEAD -> hola) [Sergio Gómez]
    |\
    | * 9c85275 2013-06-16 | Programa interactivo (master) [Sergio Gómez]
    * | 9c6ac06 2013-06-16 | Merge branch 'master' into hola [Sergio Gómez]
    |\|
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * | 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
    * | 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

Para ello podemos usar la orden `git reset` que nos permite mover HEAD donde queramos.

```bash
    $ git reset --hard 9862f33
    HEAD is now at 9862f33 hola usa la clase HolaMundo
```

2. *El estado después de deshacer los `merge`:*

```bash
 $ git hist --all
    * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD, hola) [Sergio Gómez]
    * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    | * 9c85275 2013-06-16 | Programa interactivo (master) [Sergio Gómez]
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

3. *Vamos a hacer un rebase. Continuamos en la rama `hola` y ejecutamos lo siguiente :*

```bash
 $ git rebase master
    Auto-merging lib/hola.php
    CONFLICT (content): Merge conflict in lib/hola.php
    error: could not apply 9862f33... hola usa la clase HolaMundo
    hint: Resolve all conflicts manually, mark them as resolved with
    hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
    hint: You can instead skip this commit: run "git rebase --skip".
    hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
    Could not apply 9862f33... hola usa la clase HolaMundo

```

4. *Resolvemos el conflicto del archivo `hola.php` :*

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
require('HolaMundo.php');

print "Introduce tu nombre:";
$nombre = trim(fgets(STDIN));
print new HolaMundo($nombre);
?>
```

5. *Añadimos los cambios en `staging` :* 

```bash
  $ git add lib/hola.php
    $ git status
    interactive rebase in progress; onto 269eaca
    Last commands done (2 commands done):
        pick 4e0f425 Añadida clase HolaMundo
        pick 9862f33 hola usa la clase HolaMundo
    No commands remaining.
    You are currently rebasing branch 'hola' on '8ca56f7'.
        (all conflicts fixed: run "git rebase --continue")

    Changes to be committed:
        (use "git restore --staged <file>..." to unstage)
	        modified:   lib/hola.php  
```

6. *En esta ocasión, no tenemos que hacer `$git commit`, sino continuar con el `rebase` :*

```bash
 $ git rebase --continue
    [detached HEAD 9862f33] hola usa la clase HolaMundo
    1 file changed, 3 insertions(+), 1 deletion(-)
    Successfully rebased and updated refs/heads/hola.
```

7. *El árbol tiene un aspecto distinto y mucho más limpio :*

```bash
 $ git hist --all
    * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD -> hola) [Sergio Gómez]
    * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    * 9c85275 2013-06-16 | Programa interactivo (master) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

## **4.3 Mezclando con la rama master**

Una vez hemos terminado de implementar los cambios en la rama secundaria, llevamos los cambios a la rama principal.

1. *Cambiamos a la rama `master` :*

```bash
 $ git checkout master
    Switched to branch 'master'
```

### **4.3.1 Merge fast-forward**

2. *Usamos `git merge` de tipo `fast-forward` para hacer una fusión normal y que no deja rastro :*

```bash
$ git merge hola
    Updating c3e65d0..491f1d2
    Fast-forward
     lib/HolaMundo.php | 16 ++++++++++++++++
     lib/hola.php      |  4 +++-
     2 files changed, 19 insertions(+), 1 deletion(-)
     create mode 100644 lib/HolaMundo.php
```

3. *Comprobamos el árbol :*

```bash
$ git hist --all
     * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD -> master, hola) [Sergio Gómez]
     * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
     * 9c85275 2013-06-16 | Programa interactivo [Sergio Gómez]
     * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
     * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
     * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
     * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
     * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
     * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
     * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

### **4.3.2 Merge --no-fast-forward (--no-ff)**

2. *Reseteamos `master` al estado `"Programa interactivo"` :*

```bash
$ git reset --hard 9c85275
```

3. *Comprobamos el árbol :*

```bash
 $ git hist
    * 9c85275 2013-06-16 | Programa interactivo (master) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

4. *Usamos `git merge` de tipo `--no-fast-forward` (`--no-ff`) para hacer una fusión que deja rastro de las dos ramas :*

```bash
$ git merge -m "Aplicando los cambios de la rama hola" --no-ff hola
    Merge made by the 'recursive' strategy.
     lib/HolaMundo.php | 16 ++++++++++++++++
     lib/hola.php      |  4 +++-
     2 files changed, 19 insertions(+), 1 deletion(-)
     create mode 100644 lib/HolaMundo.php
```

5. *Comprobamos el árbol :*

```bash
 $ git hist --all
    *   2eab8ca 2013-06-16 | Aplicando los cambios de la rama hola (HEAD -> master) [Sergio Gomez]
    *\
    | * 9862f33 2013-06-16 | hola usa la clase HolaMundo (hola) [Sergio Gómez]
    | * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 9c85275 2013-06-16 | Programa interactivo (master) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

En la siguiente imagen se puede ver la diferencia:

![Diferencias entre tipos de fusión](img/gitlab-merge-ff.png)

# **5 Github**

## **5.1 Tu clave pública/privada**

Muchos servidores Git utilizan autenticación mediante claves SSH. Para generar una nueva clave:

```bash
$ ssh-keygen -t rsa -C "Cuenta GitHub"
```

Esta clave se almacenará en tu sistema para permitir la autenticación con repositorios remotos. Asegúrate de no compartir la clave privada con nadie, ya que esta es tu identificador ante los servidores.

## **5.2 Configuración**

Agrega tu clave SSH generada anteriormente en GitHub:

1. Ve a Settings en tu perfil de GitHub.
2. Selecciona SSH and GPG Keys.
3. Añade una nueva clave copiando el contenido de _`~/.ssh/id_rsa.pub`_ y pega en el campo Key.
4. Dale un nombre a la clave en Title y guarda.

## **5.3 Crear un repositorio**

Para crear un repositorio en GitHub:

1. Haz clic en el signo `+` en la barra superior y selecciona New repository.
2. Dale un nombre, por ejemplo: `taller-de-git`.
3. Para sincronizar un repositorio existente en tu equipo:

```bash
Copiar código
$ git remote add origin git@github.com:usuario/taller-de-git.git
$ git branch -M main
$ git push -u origin main
```

## **5.4 Clonar un repositorio**

Para clonar un repositorio desde GitHub en tu equipo local:

```bash
$ git clone git@github.com:usuario/taller-de-git.git
$ cd taller-de-git
```

Este comando descarga todos los archivos del repositorio y te permite comenzar a trabajar con ellos en tu equipo.

## **5.5 Ramas remotas**

Podemos ver las ramas remotas para comprobar la sincronización de nuestro repositorio local con el remoto. Usa el comando:

```bash
    $ git remote show origin
    * remote origin
      Fetch URL: git@github.com:sgomez/taller-de-git.git
      Push  URL: git@github.com:sgomez/taller-de-git.git
      HEAD branch: main
      Remote branch:
        main tracked
      Local ref configured for 'git push':
        main pushes to main (up to date)
```

Te mostrará información sobre fetch (para traer cambios) y push (para enviar cambios). La rama principal se suele llamar `origin/main`.

## **5.6 Enviando actualizaciones**

Cuando realizas cambios en tu repositorio local, asegúrate de enviarlos a GitHub:

```bash
$ git add archivo
$ git commit -m "Descripción del cambio"
$ git push
```

Si necesitas vincular la rama local con una remota, utiliza:

```bash
$ git push -u origin main
```

Esto facilita futuros push sin tener que especificar el origen.

## **5.7 Recibiendo actualizaciones**

Para actualizar tu repositorio local con los últimos cambios hechos en GitHub:

1. *Trae la información de los cambios realizados en GitHub con `git fetch`, pero no importa directamente los cambios en local.*

```bash
$ git fetch
```

2. *Incorporar los cambios de la rama remota en la local, usando `git merge` o `git rebase`.*

```bash
$ git merge origin/main
```

También puedes combinar ambos pasos con `git pull` que realiza las dos acciones simultáneamente :

```bash
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 811 bytes | 811.00 KiB/s, done.
From github.com:sgomez/taller-de-git
   cbaf831..d8922e4  main     -> origin/main
Updating 6e4cda3..bb3dc3e
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

## **5.8 Problemas de sincronización**

### **5.8.1 No puedo hacer push**

Al intentar subir cambios nos podemos encontrar un mensaje como este:

```bash
$ git push
git push
To git@github.com:sgomez/taller-de-git.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'git@github.com:sgomez/taller-de-git.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

La causa es que el repositorio remoto también se ha actualizado y nosotros aún
no hemos recibido esos cambios. Es decir, ambos repositorios se han actualizado
y el remoto tiene preferencia. Hay un conflicto en ciernes y se debe resolver
localmente antes de continuar.

1. *Trae los cambios y resuelve las diferencias de los archivos :*

```bash
$ git fetch
```

2. *Aplicas tus commits por encima de los cambios traídos. Si hay conflictos, tendrás que resolverlos antes de continuar :*

```bash
$ git rebase origin/main
```

3. *Una vez resueltos los conflictos, puedes finalizar el rebase con :*

```bash
$ git rebase --continue
```

4. *Finalmente, cuando el rebase haya terminado y todos los conflictos se hayan resuelto, ya puedes hacer push :*

```bash
$ git push
```

**Nota:** Si estás colaborando con otras personas, merge suele ser una opción más segura, ya que no reescribe la historia. Sin embargo, rebase puede ser útil si deseas mantener una historia más limpia y fácil de seguir.

### **5.8.2 No puedo hacer pull**

Cuando intentas hacer pull para traer cambios del repositorio remoto, pero tienes cambios locales sin confirmar en tu espacio de trabajo. Esto puede dar lugar a mensajes de error como los siguientes:

```bash
$ git pull
error: Cannot pull with rebase: You have unstaged changes.
```

o

```bash
$ git pull
error: Cannot pull with rebase: Your index contains uncommitted changes.
```

**Causa del problema:**
Este error se produce porque Git necesita un espacio de trabajo limpio para poder integrar los cambios remotos. Si tienes cambios locales sin confirmar (modificaciones no guardadas mediante commit), Git no puede garantizar que esos cambios no entren en conflicto con los cambios que está trayendo desde el repositorio remoto.

**Solución:**
Para resolver este problema tienes varias opciones:

1. *Confirmar los cambios locales (commit) :*

La opción más sencilla es confirmar tus cambios locales para que Git pueda hacer pull sin problemas:

```bash
$ git add .
$ git commit -m "Descripción de los cambios locales"
$ git pull
```

Esto guardará tus cambios en un nuevo commit, permitiendo que Git traiga los cambios remotos y luego los mezcle con los tuyos.

2. *Guardar los cambios locales temporalmente con stash :*

Si no deseas hacer un commit porque tus cambios no están listos, puedes usar stash para guardar temporalmente los cambios y así dejar tu espacio de trabajo limpio para poder hacer el pull:

```bash
$ git stash save "Cambios temporales"
$ git pull
$ git stash pop
```

* `$git stash save` guarda los cambios actuales en una especie de pila temporal llamada stash.
* `$git pull` trae los cambios remotos.
* `$git stash pop` restaura los cambios que habías guardado en el stash.

En caso de que haya conflictos después de hacer _`stash pop`_, deberás resolverlos manualmente como en cualquier otra operación de merge.

3. *Combinar stash con pull automáticamente :*

Git también permite combinar estas acciones de forma automática. Puedes usar la opción _`--autostash`_ para hacer `pull` y `stash` en un solo paso:

```bash
$ git pull --autostash
```

Esta opción guarda tus cambios en el stash, realiza el _`pull`_ y luego aplica los cambios nuevamente desde el stash. Es una forma rápida de realizar ambas acciones cuando estás trabajando con cambios sin confirmar.

# **6 Flujo de trabajo en GitHub**

## **6.1 Paso 0. Abrir una incidencia (issue)**

El trabajo suele empezar con una incidencia o **issue**. Para ello, se usa la sección de _Issues_ en GitHub. Una **issue** es básicamente una solicitud para resolver un problema o añadir una nueva característica. Al abrir una issue, se le asigna un título, una descripción (escrita en Markdown), y opcionalmente etiquetas, hitos, responsables, etc.

**Ejemplo**: Crear una issue titulada "Crear archivo de autores" para crear un archivo `AUTHORS.md` que contenga la lista de desarrolladores del proyecto.

## **6.2 Paso 1. Crear una rama**

Crear una rama es el primer paso para empezar a implementar los cambios relacionados con la **issue**. Para cada nueva característica o corrección de errores, se debe crear una rama separada. El nombre de la rama debe seguir un formato consistente, por ejemplo:

```bash
$ git checkout -b feature-1/create-changelog
```

Después de crear la rama, se sube al repositorio remoto con:

```bash
$ git push -u origin feature-1/create-changelog
```

Esto crea una nueva rama en el repositorio remoto y permite crear un **Pull Request (PR)** desde GitHub.

## **6.3 Paso 2. Crear commits**

Trabaja en los cambios que necesitas y crea **commits** para documentar el progreso:

```bash
$ git add AUTHORS.md
$ git commit -m "Añadido fichero de autores"
```

Cada vez que hagas un cambio importante, confirma con `commit` y luego sube la rama con `push`:

```bash
$ git push
```

Los commits aparecerán en el **Pull Request** abierto en GitHub para revisión.

## **6.4 Paso 3. Discutir**

En GitHub, otros desarrolladores pueden revisar y discutir el **Pull Request**. Esto ayuda a garantizar la calidad del código y facilita la colaboración. Puedes etiquetar a revisores o asignar personas para la revisión del PR.

**Pull Requests** también permiten comentarios en línea sobre fragmentos de código específicos, lo que facilita una discusión detallada.

## **6.5 Paso 4. Desplegar**

Una vez que se ha revisado el **Pull Request** y todos están de acuerdo, es momento de integrarlo en la rama principal. GitHub ofrece tres formas para hacer el **merge** de una rama:

![Cómo cerrar un Pull Request](img/github-flow-merge.png)

### **6.5.1 Crear un merge commit**

Este método fusiona la rama en la rama principal (`main`) sin eliminar la historia de los commits individuales:

```bash
$ git checkout main
$ git merge --no-ff feature-1/create-changelog
$ git push
```

Este enfoque mantiene el historial de todos los cambios realizados en la rama.

> **Info**: GitHub siempre desactiva el _fast forward_.

### **6.5.2 Crear un rebase y merge**

Rebase garantiza que los cambios de la rama se apliquen sobre los últimos cambios de `main`, creando una historia de commits más lineal:

```bash
$ git rebase main
$ git checkout main
$ git merge --no-ff feature-1/create-changelog
$ git push
```

Este método reordena los commits para que parezca que la característica se desarrolló al final de los cambios ya en `main`.

### **6.5.3 Crear un squash commit y un merge**

El método de **squash and merge** combina todos los commits de la rama en un solo **commit** en la rama principal:

```bash
$ git checkout main
$ git merge --squash feature-1/create-changelog
$ git push
```

Este enfoque es útil para mantener el historial de `main` más limpio, ya que todos los cambios se registran como un único commit, lo cual es ideal cuando la rama incluye muchos commits pequeños.

Vamos a seleccionar este último (squash and merge) y le damos al botón para activarlo. Nos saldrá una caja para que podamos crear una descripción del commit y le damos a confirmar.

![Pull Request cerrado](img/github-flow-merge.png)

Ya hemos terminado y nos aparecerá una opción para borrar la rama, lo más recomendado para no tener ramas obsoletas.

Las consecuencias de esta acción son las siguientes:

1. El PR aparecerá como estado _merged_ y en la lista de PR como cerrado.
2. El _issue_ que abrimos se habrá cerrado automáticamente.
3. En el listado de commits aparecerá solo uno con un enlace al PR (en vez de los dos commits que hicimos).

## **6.6 Paso 5. Sincronizar**

Hemos cambiado el repositorio en GitHub, pero nuestra rama `main` local no contiene los mismos cambios que el de `origin`. Así que nos toca sincronizar y borrar la rama obsoleta:

```bash
$ git checkout main
$ git pull --rebase --autostash
$ git branch -D feature-1/create-changelog
```

> **Info**:  
> ¿Por qué _squash and merge_ y no un _merge_ o _rebase_? De nuevo depende de los gustos de cada equipo de desarrollo. Las características de _squash_ es que elimina (relativamente) rastros de errores intermedios mientras se implementaba la rama, deja menos commits en la rama _main_ y nos enlaza al PR donde se implementaron los cambios.

Para algunas personas estas características son unas ventajas, para otras no. Lo mejor es experimentar cada opción y que cada uno decida cómo quiere trabajar.

# **7 Colaboración en GitHub**

## **7.1 Clonar un repositorio**

Para colaborar en un proyecto existente, primero es necesario clonar el repositorio a nuestro espacio de trabajo local. Primero vamos a la web del proyecto y hacemos un "fork" para tener una copia propia. Luego, clonamos nuestro fork localmente:

```bash
$ git clone git@github.com:miusuario/miniblog.git
```

### **7.1.1 Sincronizar con el repositorio original**

Cuando hacemos un "fork" de un proyecto, se crea una copia que no se actualiza con los cambios del proyecto original de manera automática. Para mantener nuestro fork actualizado, es necesario agregar el repositorio original como remoto:

```bash
$ git remote add upstream git@github.com:sgomez/miniblog.git
```

Luego, para incorporar cambios del repositorio original, usamos:

```bash
$ git fetch upstream
$ git merge upstream/master
```

### **7.1.2 Creando nuevas funcionalidades**

Para crear una nueva funcionalidad o arreglar un error, primero creamos una nueva rama:

```bash
$ git checkout -b add-license
```

Luego, añadimos el código necesario y lo confirmamos con un commit:

```bash
$ echo "LICENCIA MIT" > LICENSE
$ git add LICENSE
$ git commit -m "Añadido archivo de licencia de uso"
```

Finalmente, enviamos los cambios a nuestro repositorio en GitHub:

```bash
$ git push --set-upstream origin add-license
```

En GitHub, podemos crear un "Pull Request" para sugerir los cambios al repositorio original.

### **7.1.3 Todo esto es algo complicado...**

El trabajo en Git puede parecer complejo al principio, especialmente al manejar ramas y colaborar con repositorios remotos. La clave está en entender cada nivel de complejidad antes de avanzar al siguiente, desde los comandos básicos como `add`, `commit` y `checkout`, hasta los más avanzados como `merge`, `rebase` y `push`.

## **7.2 Último paso, documentación.**

La documentación es esencial para que otros colaboradores entiendan el proyecto. Esto se puede hacer mediante un archivo `README.md`, una wiki o incluso una página web para el proyecto.

# **8 Otros comandos de Git**

## **8.1 Git stash (reserva)**

La orden `git stash` nos permite salvar momentáneamente el espacio de trabajo cuando tenemos que cambiar de rama o preparar la rama actual para sincronizar cambios.

Las operaciones más importantes que podemos hacer con `git stash` son:

### **8.1.1 git stash save**

Es equivalente a poner solo `git stash` pero nos permite realizar más acciones como:

```bash
git stash save "Tu mensaje"
git stash save -u
```

El parámetro `-u` permite que se almacenen también los ficheros sin seguimiento previo (_untracked_ en inglés, aquellos ficheros que no se han metido nunca en el repositorio).

### **8.1.2 git stash list**

Permite mostrar la pila del stash.

```bash
$ git stash list
stash@{0}: On master: Stash con mensaje
stash@{1}: WIP on master: 4ab21df First commit
```

### **8.1.3 git stash apply**

Esta orden coge el stash que está arriba en la pila y lo aplica al espacio de trabajo actual. En este caso siempre es `stash@{0}`. El stash permanece en la pila.

Se puede indicar como parámetro un stash en concreto.

### **8.1.4 git stash pop**

Funciona igual que `git apply` con la diferencia de que el stash sí se borra de la pila.

### **8.1.5 git stash show**

Muestra un resumen de los ficheros que se han modificado en ese stash.

```bash
$ git stash show
A.txt | 1 +
B.txt | 3 +++
2 file changed, 4 insertions(+)
```

Para ver los cambios podemos usar el parámetro `-p`

```bash
$ git stash show -p
--- a/A.txt
+++ b/A.txt
@@ -45,6 +45,7 @@ nav:
+ This is a change
```

Por defecto siempre muestra la cabeza de la pila. Igual que en casos anteriores podemos indicar un stash en concreto.

```bash
$ git stash show stash@{1}
```

### **8.1.6 git stash branch**

Permite crear una nueva rama a partir del último stash. Además, el mismo es borrado de la pila. Se puede especificar uno en concreto si lo queremos, como en el resto de comandos.

```bash
git stash branch nombre-de-nueva-rama stash@{1}
```

### **8.1.7 git stash clear**

Este comando borra todos los stash de la pila. Es destructiva y no se puede deshacer.

### **8.1.8 git stash drop**

Permite borrar un stash en concreto (o el último si no se indica ninguno). Como con `clear`, borrarlo implica que no se puede recuperar.

## **8.2 Git worktree**

Uno de los problemas más habituales es tener que tocar una rama distinta a la que tenemos actualmente. Eso implica que si estamos en medio de un trabajo tendríamos que hacer un commit o un stash, lo cual a veces es bastante molesto.

Con `git worktree` podemos crear un directorio de trabajo que contenga otra rama distinta, de forma temporal. No supone otro clon del repositorio porque ambos usan el mismo.

### **8.2.1 git worktree add**

Esta función es la que crea el espacio de trabajo temporal. Imaginemos que estamos en una rama llamada `develop`:

```bash
$ git worktree add ../project-master master
$ git worktree add -b fix ../project-fix master
```

La primera orden crea un directorio llamado `project-master` que contiene el estado de `master`. La segunda, que contiene el parámetro `-b` equivale a crear una nueva rama llamada `fix`, que se crea desde `master` (suponemos que no existe `fix`).

### **8.2.2 git worktree list**

Muestra el listado de directorios y espacios de trabajo.

```bash
$ git worktree list
/home/sergio/taller-de-git  3b63b4b [master]
/home/sergio/fix           3b63b4b [fix]
```

### **8.2.3 git worktree remove**

Borra un espacio de trabajo. Hay que indicar el nombre entre corchetes que aparece en el listado.

```bash
$ git worktree delete fix
```

### **8.2.4 git worktree prune**

Una cuestión importante es que las ramas que estén desplegadas en otro espacio de trabajo se encuentran bloqueadas y no se pueden desbloquear en otro distinto.

Esto significa que si estamos trabajando en la rama `developer` y creamos otro `worktree` en otro directorio de la rama `master`, no podemos pasar a `master`. No es posible tener la misma rama en varios espacios de trabajo.

Si se ha borrado el directorio a mano (en vez de usando `remove`), eso no implica que el bloqueo desparezca. Con esta orden podemos hacer que Git compruebe que los espacios de trabajo secundarios siguen existiendo y se elimine el bloqueo.

## **8.3 Git blame**

Lo ideal en un equipo de desarrollo es que el código pase por todas las manos para así mejorar su calidad.

Con `git blame` podemos saber quién fue el último en modificar una línea concreta de código, en qué commit y en qué fecha lo hizo.

```bash
$ git blame ejemplo.php
33cdd02c (Sergio Gómez 2020-01-20 16:58:52 +0100  8)   name: "material"
33cdd02c (Sergio Gómez 2020-01-20 16:58:52 +0100  9)   language: "es"
```