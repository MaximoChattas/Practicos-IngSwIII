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

## Paso 7
### Comandos para Dockerfile
Se describen a continuación algunos comandos que pueden ser usados al momento de escribir un archivo tipo Dockerfile.
- FROM: toma como base una imagen existente.
- RUN: ejecuta un comando.
- ADD: agrega un directorio o archivo local.
- COPY: copia archivos y directorios.
- EXPOSE: especifica los puertos en los que escucha la aplicación.
- CMD: especifica comandos por defecto.
- ENTRYPOINT: especifica el ejecutable.

### Obteniendo el código fuente y crear una imagen
En primer lugar, clonamos el repositorio que contiene el código a partir del cuál se construirá la nueva imagen.

![Imagen Paso 7a](Paso%207a.jpg)

Mediante el comando `docker build -t mywebapi .` se construye una imagen llamada "mywebapi" con todos los archivos del directorio actual. Esto ejecuta todos los comandos indicados en el archivo Dockerfile.

![Imagen Paso 7b](Paso%207b.jpg)

### Explicación de Dockerfile
A continuación, se explica en detalle cada línea del Dockerfile en el repositorio clonado.
```
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
```
Utiliza en esta primera etapa la imagen de Microsoft .NET ASP.NET Core 7.0 utilizada para ejecutar aplicaciones ASP.NET.

```
WORKDIR /app
```
Establece el directorio /app como directorio de trabajo, de modo que los siguientes comandos serán ejecutados en este mismo.

```
EXPOSE 80
EXPOSE 443
EXPOSE 5254
```
Expone los puertos 80, 443 y 5254 para conexiones externas.

```
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
```
Utiliza la imagen oficial del SDK de .NET 7.0, necesaria para compilar y publicar la aplicación.

```
WORKDIR /src
```
Establece el directorio /src como directorio de trabajo, de modo que los siguientes comandos serán ejecutados en este mismo.

```
COPY ["SimpleWebAPI/SimpleWebAPI.csproj", "SimpleWebAPI/"]
```
Copia el archivo .csproj desde el dispositivo local al contenedor y lo ubica en el directorio SimpleWebAPI.


```
RUN dotnet restore "SimpleWebAPI/SimpleWebAPI.csproj"
```
Ejecuta el comando que permite restaurar las dependencias y paquetes necesarios para construir el proyecto.

```
COPY . .
```
Copia todos los archivos en la ubicación actual al contenedor.

```
WORKDIR "/src/SimpleWebAPI"
```
Establece el directorio /src/SimpleWebAPI como directorio de trabajo, de modo que los siguientes comandos serán ejecutados en este mismo.

```
RUN dotnet build "SimpleWebAPI.csproj" -c Release -o /app/build
```
Corre el comando para construir la aplicación en modo release y guarda los archivos resultantes en el directorio /app/build.

```
FROM build AS publish
```
Comienza una nueva etapa (publish) basada en la anterior (build).

```
RUN dotnet publish "SimpleWebAPI.csproj" -c Release -o /app/publish /p:UseAppHost=false
```
Publica la aplicación, almacenando los archivos para ejecutar la aplicación en el directorio /app/publish.

```
FROM base AS final
```
Etapa final del contenedor, basada en la imagen base previamente definida.

```
WORKDIR /app
```
Cambia nuevamente el directorio de trabajo a /app.

```
COPY --from=publish /app/publish .
```
Copia todos los archivos generados durante la etapa publish al directorio /app/publish.

```
ENTRYPOINT ["dotnet", "SimpleWebAPI.dll"]
```
Establece el punto de entrada al contenedor: `dotnet SimpleWebAPI.dll`

```
#CMD ["/bin/bash"]
```
Línea comentada, establecería que el contenedor ejecute el comando `/bin/bash` al iniciar.

### Crear un contenedor con la imagen
En primer lugar, se verifican las imagenes disponibles en el dispositivo de forma local mediante el comando `docker images`
Como se puede ver, la imagen mywebapi construida anteriormente, se encuentra disponible para ser utilizada.

![Imagen Paso 7c](Paso%207c.jpg)

Luego, se crea un contenedor utilizando la imagen mywebapi mediante el comando `docker run mywebapi`.
Se muestra a continuación que fue creado un nuevo contenedor "upbeat_carson" que utiliza la imagen mywebapi.

![Imagen Paso 7d](Paso%207d.jpg)

### Subir la imagen a Docker Hub
Para subir la imagen a Docker Hub, se utilizaron los siguientes comandos:

1. `docker login` : inicio de sesión en Docker Hub desde la terminal.
2. `docker tag mywebapi maxichattas/mywebapi:latest` : etiqueta la imagen con el nombre de usuario "maxichattas", nombre de imagen "mywebapi" y tag "latest".
3. `docker push maxichattas/mywebapi:latest` : sube la imagen a Docker Hub.
4. `docker pull maxichattas/mywebapi:latest` : se verifica que la imagen se haya subido correctamente, al intentar descargarla desde Docker Hub.

![Imagen Paso 7e](Paso%207e.jpg)

Se adjunta además un link directo a la imagen subida en la plataforma:
[My Web API](https://hub.docker.com/r/maxichattas/mywebapi)

## Paso 8
Se crea un nuevo contenedor con la imagen mywebapi a través del comando `docker run --name myapi -d mywebapi`. Además, usando el comando `docker ps` se observa que el contenedor expone los puertos 80, 443 y 5254.

![Imagen Paso 8a](Paso%208a.jpg)

Por más que los puertos están expuestos, no es posible acceder desde el navegador, ya que los puertos expuestos no están mapeados a los puertos del dispositivo. Por este motivo, se elimina el contenedor previamente creado y lo creamos nuevamente configurando correctamente los puertos mediante el comando: `docker run --name myapi -d -p 80:80 mywebapi`

![Imagen Paso 8b](Paso%208b.jpg)

De este modo, ingresando a [http://localhost/WeatherForecast](http://localhost/WeatherForecast) podemos ver los datos resultantes de la API.

![Imagen Paso 8c](Paso%208c.jpg)

## Paso 9
Luego de modificar el Dockerfile de la siguiente manera:
```
# ENTRYPOINT ["dotnet", "SimpleWebAPI.dll"]
CMD ["/bin/bash"]
```

Se reconstruye la imagen mediante el comando: `docker build -t mywebapi .`

![Imagen Paso 9a](Paso%209a.jpg)

De este modo, al iniciar el contenedor, la API no se ejecuta automáticamente, por lo que es necesario correr el comando `dotnet SimpleWebAPI.dll` en la consola del contenedor.

![Imagen Paso 9b](Paso%209b.jpg)

Finalmente, salimos del contenedor ejecutando `^C` para detener la API y se ejecuta el comando `exit`.

![Imagen Paso 9c](Paso%209c.jpg)

## Paso 10
A continuación, para montar un volúmen dentro del contenedor, se selecciona el directorio local con path:
```
/Users/maxichattas/Documents/UCC/4º Año/Ingeniería de Software III/Práctico/Prácticos/Container Volume
```

Se muestra a continuación cómo se montó el volúmen en el contenedor, y los comandos ejecutados para crear un archivo en este directorio desde la consola del contenedor.

![Imagen Paso 10a](Paso%2010a.jpg)

Como se puede ver, el archivo "hola.txt" fue creado tanto en el contenedor, como en el directorio local del dispositivo.
- Desde el contenedor:

![Imagen Paso 10b (Container)](Paso%2010b%20(Container).jpg)

- Desde el dispositivo:

![Imagen Paso 10b (Host)](Paso%2010b%20(Host).jpg)

Nota: la diferencia de horario entre la creación de ambos archivos se debe a que el contenedor tiene un huso horario diferente al dispositivo host.