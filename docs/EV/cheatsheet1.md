`git config --global user.name "John Doe"` -> establecer nombre de usuario 
`git config --global user.email johndoe@example.com` -> establecer dirección de correo electrónico

`git status` -> consultar el estado de los archivos 
`git init` -> crear un nuevo repositorio 
`nano [nombre_archivo]` -> crear archivo y entrar en él 
`git add [nombre_archivo]` -> añadir el archivo al área de preparación 
`git commit -m "[texto]"` -> el archivo pasa a local repository y se le asigna un código de inserción 
`git commit -a -m "[texto]"` -> hace el add y después el commit

`git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"` -> crear el alias hist con la configuración establecida 
`git hist --all` -> listar los cambios en todas las ramas

`git checkout [hash]` -> regresar al momento del estado del proyecto que se indica 
`git checkout [rama]` -> moverse a la rama 
`git checkout -b [nombre]` -> crear rama y acceder a ella 
`git switch -` -> volver a la rama anterior 
`git branch` -> listar las ramas que hay 
`git branch [nombre]` -> crear rama 
`git tag [nombre]` -> etiquetar versiones en la historia del repositorio 
`git tag -d [nombre_etiqueta]` -> borrar etiqueta

`git merge [nombre_rama]` -> fusionar ramas 
`git rebase [nombre_rama]` -> fusionar ramas 
`git rebase --continue` -> continuar el rebase 
`git merge --no-ff [nombre_rama]` -> fusionar sin hacer fast-forward

`git restore [nombre_archivo]` -> restaurar archivo 
`git reset --hard [hash]` -> mover el HEAD al hash indicado

`ssh-keygen -t rsa -C "Cuenta GitHub"` -> crear una nueva clave
Meterse en las claves de GitHub y al crear la clave, en el apartado key volcar lo que hay en el archivo `~/.ssh/id_rsa.pub`

`git clone [url_repositorio]` (ej: `git@github.com:sgomez/taller-de-git.git`) -> clonar el repositorio 
`git remote add [nombre_repositorio_remoto] [url_repositorio_remoto]` -> añadir conexión remota 
`git remote show [nombre_repositorio_remoto]` -> ver configuración del repositorio remoto 
`git fetch` -> traer la información desde el servidor remoto 
`git pull` -> hace un fetch y un merge a la vez (traer de GitHub y meterlo en nuestro local) 
`git pull --autostash --rebase` -> ejecutar si no funciona el git pull 
`git push` -> enviar cambios al repositorio remoto 
`git push -u [repositorio] [rama]` (ej: origin main) -> enviar los cambios de la rama del repositorio local al repositorio remoto

`git mv [archivo] [directorio]` -> mover el archivo al directorio
`cat [nombre_archivo]` -> ver contenido de un archivo
