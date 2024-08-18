# Trabajo Práctico 2

## Paso 1
Se muestra la versión de Docker instalada en mi dispositivo.

![Imagen Paso 1](Paso%201.jpg)

## Paso 2
Se muestra mi perfil en Docker Hub.

![Imagen Paso 2](Paso%202.jpg)

## Paso 3
Se descarga la imagen "busybox" y se muestra la misma dentro del listado de imágenes descargadas localmente.

![Imagen Paso 3](Paso%203.jpg)

## Paso 4
Se realiza un primer intento de correr un contenedor con la imagen de busybox sin ningún parámetro.

![Imagen Paso 4a](Paso%204a.jpg)

En la ventana de terminal, no se obtiene ningún resultado visible. Esto se debe a que la imagen de busybox por sí misma, sin ningún parámetro especificado, no tiene ninguna funcionalidad. Aún así, el contenedor con esta imagen se crea, como se muestra a continuación.

![Imagen Paso 4b](Paso%204b.jpg)

A continuación, se intenta correr nuevamente el contenedor, pero esta vez con algún parámetro específico.

![Imagen Paso 4c](Paso%204c.jpg)

Como se puede ver, se imprime en la consola el mismo texto que el escrito luego del comando "echo".

Mediante el comando `docker ps` se ven todos los contenedores que se encuentran en estado "Running", mientras que si se agrega el modificador "-a" (`docker ps -a`) se pueden ver todos los contenedores del dispositivo, incluso si los mismos se encuentran detenidos. Este comando muestra como salida una tabla que contiene toda la información sobre los contenedores, incluyendo su nombre, imagen, ID, estado actual y más.

![Imagen Paso 4d](Paso%204d.jpg)

## Paso 5
A continuación, se utiliza el modificador "-it" para correr un contenedor en modo interactivo. Esto permite ejecutar comandos en la consola dentro de sistema operativo del contenedor.
`docker run -it busybox sh`

A continuación se demuestra esto mismo corriendo algunos comandos en la terminal del contenedor, tales como:
- `ls`
- `uptime`
- `free`
- `ps`
- `ls -l /`

![Imagen Paso 5](Paso%205.jpg)

Finalmente, salimos del contenedor mediante el comando `exit`.

## Paso 6
Se muestra a continuación cómo liberar espacio eliminando contenedores no utilizados.

En primer lugar, mediante el comando `docker ps -a`, obtenemos una lista de todos los contenedores en el dispositivo. Luego, utilizamos el comando `docker rm [nombre o hash]` para borrar un contenedor específico (se corre nuevamente el primer comando para mostrar que el contenedor fue eliminado exitósamente).

Finalmente, utilizamos el comando `docker container prune` para eliminar todos los contenedores que no se encuentren corriendo en ese momento, mostrando a continuación que todos los contenedores fueron eliminados correctamente.

![Imagen Paso 6](Paso%206.jpg)