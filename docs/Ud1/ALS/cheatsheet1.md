---
title: 'CH1 - Adrian Leonard Stan'
---

# 🛠️ Git Cheatsheet Completo

## 📅 Configuración Inicial

# Configurar el nombre de usuario
git config --global user.name "Tu Nombre"

# Configurar el correo electrónico
git config --global user.email "tu.email@example.com"

# Ver la configuración actual
git config --list

📦 Comandos Básicos

Copiar código
# Inicializar un nuevo repositorio
git init

# Clonar un repositorio existente
git clone <url-del-repositorio>

# Ver el estado de los archivos
git status

# Agregar archivos al índice (staging area)
git add <archivo>        # Agregar un archivo específico
git add .                # Agregar todos los archivos

# Hacer un commit
git commit -m "Mensaje del commit"

# Ver el historial de commits
git log
🌿 Ramas

Copiar código
# Crear una nueva rama
git branch <nombre-rama>

# Listar todas las ramas
git branch

# Cambiar a otra rama
git checkout <nombre-rama>

# Crear y cambiar a una nueva rama
git checkout -b <nombre-rama>

# Fusionar otra rama a la rama actual
git merge <nombre-rama>

# Eliminar una rama
git branch -d <nombre-rama>
🗄️ Stash

Copiar código
# Guardar cambios no confirmados
git stash

# Guardar cambios con mensaje
git stash save "Tu mensaje"

# Ver la lista de stashes
git stash list

# Aplicar el último stash
git stash apply

# Aplicar y eliminar el último stash
git stash pop

# Mostrar cambios en el stash
git stash show          # Resumen
git stash show -p      # Detalle de cambios

# Eliminar un stash específico
git stash drop stash@{n}

# Limpiar todos los stashes
git stash clear
📁 Worktree

Copiar código
# Agregar un nuevo directorio de trabajo
git worktree add <ruta> <rama>

# Crear un nuevo worktree con una nueva rama
git worktree add -b <nueva-rama> <ruta> <rama-base>

# Listar los worktrees
git worktree list

# Eliminar un worktree
git worktree remove <nombre>
👤 Blame

Copiar código
# Mostrar quién modificó cada línea de un archivo
git blame <archivo>
🌍 Repositorio Remoto

Copiar código
# Agregar un repositorio remoto
git remote add <nombre> <url>

# Ver los repositorios remotos
git remote -v

# Enviar cambios al repositorio remoto
git push <nombre-remoto> <rama>

# Obtener cambios del repositorio remoto
git pull <nombre-remoto> <rama>

# Ver la configuración de remotos
git remote show <nombre-remoto>
⚙️ Otros Comandos Útiles

Copiar código
# Comparar cambios
git diff                # Cambios no añadidos
git diff --cached       # Cambios en el staging area

# Revertir cambios en un archivo
git checkout -- <archivo>

# Deshacer el último commit (manteniendo cambios)
git reset --soft HEAD~1

# Deshacer el último commit (perdiendo cambios)
git reset --hard HEAD~1
🔄 Resumen de Ramas en Git Flow

Copiar código
# Inicializar Git Flow
git flow init

# Crear una rama de funcionalidad
git flow feature start <nombre-feature>

# Finalizar una rama de funcionalidad
git flow feature finish <nombre-feature>

# Crear una rama de lanzamiento
git flow release start <versión>

# Finalizar una rama de lanzamiento
git flow release finish <versión>

# Crear una rama de parche
git flow hotfix start <nombre-hotfix>

# Finalizar una rama de parche
git flow hotfix finish <nombre-hotfix>