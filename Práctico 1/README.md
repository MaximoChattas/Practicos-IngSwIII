# Trabajo Práctico 1

## Paso 1
Se muestra a continuación que Git está instalado en el dispositivo.
![Imagen Paso 1](Paso%201.jpg)

## Paso 2
Se muestra a continuación como se creó un nuevo repositorio en un directorio local, se agregó un nuevo archivo llamado "Readme.md" (con una línea de texto) y luego se hizo un commit de los cambios con un mensaje.

Comandos utilizados:

1. `mkdir Repo01` : Crear directorio.
2. `git init` : Inicializa el repositorio local.
3. `touch Readme.md` : Crea el archivo.
4. `git status` : Muestra los archivos modificados.
5. `git add .` : Agrega los archivos modificados para incluirlos en el próximo commit.
6. `git commit -m "Primer commit` : Crea el commit con el mensaje especificado.

![Imagen Paso 2](Paso%202.jpg)

## Paso 3
En primer lugar, se instaló TextMate y su Shell Support.
![Imagen Paso 3a](Paso%203a.jpg)

Luego, se integra el editor con Git mediante los siguientes comandos.
![Imagen Paso 3b](Paso%203b.jpg)

## Paso 4
En primer lugar, se crea un repositorio desde github.com con el archivo Readme.md por defecto.
![Imagen Paso 4a](Paso%204a.jpg)

Clonamos el repositorio en un directorio local.
![Imagen Paso 4b](Paso%204b.jpg)

Se modifica el archivo README, se agrega un archivo gitignore (que contiene *.bak). Con estos cambios, se hace un commit y luego se suben al repositorio remoto mediante `git push origin main`
![Imagen Paso 4c](Paso%204c.jpg)

Estos cambios se pueden ver desde Github.
![Imagen Paso 4d](Paso%204d.jpg)

## Paso 5
Para este paso, se utiliza el repositorio local creado anteriormente en el [paso 2](#paso-2).

Creamos un nuevo repositorio vacío en Github.
![Imagen Paso 5a](Paso%205a.jpg)

Se asocia el repositorio local con el repositorio remoto y se suben los cambios previamente realizados a este último.
![Imagen Paso 5b](Paso%205b.jpg)

Se verifica que los cambios pueden verse desde Github.
![Imagen Paso 5c](Paso%205c.jpg)

## Paso 6
Utilizando el repositorio creado en el [paso 4](#paso-4), creamos en él una nueva rama llamada "Rama_A". Dentro de esta rama, se modificó el archivo README.md y se hizo un commit. Finalmente, se verificó la direrencia entre la rama main y esta nueva.
![Imagen Paso 6](Paso%206.jpg)

## Paso 7
### Merge Fast Forward
En primer lugar, se hace un merge Fast-Forward. Luego, se suben los cambios al repositorio remoto y se elimina la rama creada. Finalmente, se ve el log de commits.

Comandos utilizados:
1. `git merge` : fusiona ramas (mueve el puntero hacia adelante).
2. `git branch -d` : elimina la rama.
3. `git push` : sube los cambios al repositorio remoto.
4. `git log --oneline` : muestra el log de commits.
![Imagen Paso 7a](Paso%207a.jpg)

### Merge No Fast Forward
Similar al paso anterior, creamos una nueva rama (Rama_B), pero antes de cambiarnos a ella, se hace un cambio en un archivo en la rama main (y un commit del mismo). Luego, nos movemos a esta nueva rama y realizamos un cambio en el archivo gitignore, del cual también hacemos commit. Finalmente, nos movemos nuevamente a la rama main y realizamos merge, pero esta vez de tipo No Fast Forward debido a que hubo cambios en ambas ramas, y eliminamos Rama_B.
![Imagen Paso 7b](Paso%207b.jpg)

Se muestra a continuación el log de lo descripto anteriormente.
![Imagen Paso 7c](Paso%207c.jpg)

## Paso 10
Ejercicios introductorios completados.
![Imagen Paso 10](Paso%2010.jpg)