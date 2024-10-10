# Cheatsheet - Comandos Básicos de Administración en Linux

  

## 1. Actualización de Paquetes con APT

- **Actualizar lista de paquetes disponibles:**

```bash

sudo apt update

```

- **Actualizar los paquetes instalados:**

```bash

sudo apt upgrade

```

- **Eliminar paquetes innecesarios:**

```bash

sudo apt autoremove

```

  

## 2. Creación y Manipulación de Archivos y Directorios

- **Ver el directorio actual:**

```bash

pwd

```

  

## 3. Manipulación de Permisos

- **Cambiar permisos de un archivo/directorio:**

```bash

chmod [permisos] (archivo/directorio)

```

  

## 4. Gestión de Usuarios y Grupos

- **Ver lista de usuarios:**

```bash

cat /etc/passwd

```

- **Ver lista de grupos:**

```bash

cat /etc/group

```

- **Cambiar el propietario de un archivo/directorio:**

```bash

chown [usuario]:[grupo] (archivo/directorio)

```

- **Crear un grupo:**

```bash

groupadd grupo1

```

- **Cambiar el grupo de un archivo/directorio:**

```bash

chgrp [grupo] (archivo/directorio)

```

- **Añadir un usuario a un grupo:**

```bash

usermod -a -G grupo1 usuario1

```

- **Verificar los grupos a los que pertenece un usuario:**

```bash

groups usuario1

```

  

- **Eliminar un usuario de un grupo:**

```bash

sudo gpasswd -d usuario1 grupo1

```

  

- **Eliminar un grupo:**

```bash

groupdel grupo1

```

  

- **Eliminar un usuario (incluyendo su directorio personal):**

```bash

sudo userdel -r usuario1

```

  

## 5. Gestión de Procesos Básicos

- **Ver procesos de un usuario específico:**

```bash

ps -u admin

```

- **Ver procesos de un usuario en formato extendido:**

```bash

ps -u admin -f

```

- **Ejecutar un proceso en segundo plano:**

```bash

yes > /dev/null &

```

- **Ver procesos ejecutándose en segundo plano:**

```bash

jobs

```

- **Traer al primer plano el primer proceso:**

```bash

fg %1

```

- **Matar un proceso por su PID:**

```bash

kill (pid)

```

- **Ver todos los procesos del sistema:**

```bash

ps aux

```

- **Ver todos los procesos del sistema (formato extendido):**

```bash

ps -ef

```

```

Aquí tienes un **cheatsheet** en formato **Markdown** con los comandos de **Git** que mencionas:

```markdown
# Cheatsheet - Comandos Básicos de Git

## 1. Configuración de Usuario
- **Configurar nombre de usuario global:**
  ```bash
  git config --global user.name "John Doe"
  ```
- **Configurar correo electrónico global:**
  ```bash
  git config --global user.email johndoe@example.com
  ```

## 2. Clonar un Repositorio
- **Clonar un repositorio desde GitHub:**
  ```bash
  git clone git@github.com:xxxxxxx/xxxxxxx.git
  ```

## 3. Configuración de Push
- **Configurar el push como simple:**
  ```bash
  git config --global push.default simple
  ```

## 4. Historial y Alias
- **Ver el historial de commits:**
  ```bash
  git log
  ```
- **Crear alias para el historial de commits:**
  ```bash
  git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
  ```

## 5. Estado y Staging
- **Ver el estado de los archivos:**
  ```bash
  git status
  ```
- **Usar el alias para ver el historial (si se ha configurado):**
  ```bash
  git hist
  ```
- **Agregar un archivo al área de staging:**
  ```bash
  git add hola.php
  ```
- **Cambiar entre ramas recientes:**
  ```bash
  git switch -
  ```

## 6. Commit y Checkout
- **Hacer un commit con comentario:**
  ```bash
  git commit -am "Comentario aquí"
  ```
- **Restaurar un archivo desde el área de staging:**
  ```bash
  git restore --staged hola.php
  ```
- **Revertir el último commit (sin editar el mensaje):**
  ```bash
  git revert HEAD --no-edit
  ```

## 7. Reseteo y Edición de Commits
- **Resetear el repositorio a un commit específico:**
  ```bash
  git reset --hard v1  # O también usando el hash
  git reset --hard hash
  ```
- **Editar el mensaje del último commit:**
  ```bash
  git commit --amend -m "Nuevo comentario"
  ```

## 8. Ramas
- **Crear una nueva rama:**
  ```bash
  git branch hola
  ```
- **Fusionar la rama `master` con la rama actual:**
  ```bash
  git merge master
  ```
- **Fusionar sin fast-forward:**
  ```bash
  git merge --no-ff
  ```

## 9. Rebase
- **Continuar un rebase:**
  ```bash
  git rebase --continue
  ```

## 10. Remotos
- **Añadir un repositorio remoto:**
  ```bash
  git remote add origin git@github.com:Alexillo98/taller-de-git-clase.git
  ```
- **Cambiar el nombre de la rama actual a `main`:**
  ```bash
  git branch -M main
  ```

## 11. Operaciones de Sincronización
- **Empujar cambios al repositorio remoto:**
  ```bash
  git push
  git push origin master
  git push --force
  ```
- **Traer cambios desde el repositorio remoto:**
  ```bash
  git fetch
  ```
- **Actualizar y hacer pull desde el remoto:**
  ```bash
  git pull
  git pull --rebase
  ```

## 12. Stash (Guardar cambios temporalmente)
- **Guardar cambios en el stash:**
  ```bash
  git stash save
  ```
- **Sincronizar con el remoto y aplicar el stash:**
  ```bash
  git pull
  git stash pop
  ```
- **Sincronizar y aplicar stash automáticamente:**
  ```bash
  git pull --autostash
  ```

## 13. Otros Comandos
- **Eliminar un archivo del repositorio:**
  ```bash
  git rm hola.php
  ```
- **Ver detalles del remoto:**
  ```bash
  git remote show origin
  ```