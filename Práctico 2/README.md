# Trabajo Práctico 2

## Paso 1
Se muestra la versión de Docker instalada en mi dispositivo.

![Imagen Paso 1](Imágenes/Paso%201.jpg)

## Paso 2
Se muestra mi perfil en Docker Hub.

![Imagen Paso 2](Imágenes/Paso%202.jpg)

## Paso 3
Se descarga la imagen "busybox" y se muestra la misma dentro del listado de imágenes descargadas localmente.

![Imagen Paso 3](Imágenes/Paso%203.jpg)

## Paso 4
Se realiza un primer intento de correr un contenedor con la imagen de busybox sin ningún parámetro.

![Imagen Paso 4a](Imágenes/Paso%204a.jpg)

En la ventana de terminal, no se obtiene ningún resultado visible. Esto se debe a que la imagen de busybox por sí misma, sin ningún parámetro especificado, no tiene ninguna funcionalidad. Aún así, el contenedor con esta imagen se crea, como se muestra a continuación.

![Imagen Paso 4b](Imágenes/Paso%204b.jpg)

A continuación, se intenta correr nuevamente el contenedor, pero esta vez con algún parámetro específico.

![Imagen Paso 4c](Imágenes/Paso%204c.jpg)

Como se puede ver, se imprime en la consola el mismo texto que el escrito luego del comando "echo".

Mediante el comando `docker ps` se ven todos los contenedores que se encuentran en estado "Running", mientras que si se agrega el modificador "-a" (`docker ps -a`) se pueden ver todos los contenedores del dispositivo, incluso si los mismos se encuentran detenidos. Este comando muestra como salida una tabla que contiene toda la información sobre los contenedores, incluyendo su nombre, imagen, ID, estado actual y más.

![Imagen Paso 4d](Imágenes/Paso%204d.jpg)

## Paso 5
A continuación, se utiliza el modificador "-it" para correr un contenedor en modo interactivo. Esto permite ejecutar comandos en la consola dentro de sistema operativo del contenedor.
`docker run -it busybox sh`

A continuación se demuestra esto mismo corriendo algunos comandos en la terminal del contenedor, tales como:
- `ls`
- `uptime`
- `free`
- `ps`
- `ls -l /`

![Imagen Paso 5](Imágenes/Paso%205.jpg)

Finalmente, salimos del contenedor mediante el comando `exit`.

## Paso 6
Se muestra a continuación cómo liberar espacio eliminando contenedores no utilizados.

En primer lugar, mediante el comando `docker ps -a`, obtenemos una lista de todos los contenedores en el dispositivo. Luego, utilizamos el comando `docker rm [nombre o hash]` para borrar un contenedor específico (se corre nuevamente el primer comando para mostrar que el contenedor fue eliminado exitósamente).

Finalmente, utilizamos el comando `docker container prune` para eliminar todos los contenedores que no se encuentren corriendo en ese momento, mostrando a continuación que todos los contenedores fueron eliminados correctamente.

![Imagen Paso 6](Imágenes/Paso%206.jpg)

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

![Imagen Paso 7a](Imágenes/Paso%207a.jpg)

Mediante el comando `docker build -t mywebapi .` se construye una imagen llamada "mywebapi" con todos los archivos del directorio actual. Esto ejecuta todos los comandos indicados en el archivo Dockerfile.

![Imagen Paso 7b](Imágenes/Paso%207b.jpg)

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

![Imagen Paso 7c](Imágenes/Paso%207c.jpg)

Luego, se crea un contenedor utilizando la imagen mywebapi mediante el comando `docker run mywebapi`.
Se muestra a continuación que fue creado un nuevo contenedor "upbeat_carson" que utiliza la imagen mywebapi.

![Imagen Paso 7d](Imágenes/Paso%207d.jpg)

### Subir la imagen a Docker Hub
Para subir la imagen a Docker Hub, se utilizaron los siguientes comandos:

1. `docker login` : inicio de sesión en Docker Hub desde la terminal.
2. `docker tag mywebapi maxichattas/mywebapi:latest` : etiqueta la imagen con el nombre de usuario "maxichattas", nombre de imagen "mywebapi" y tag "latest".
3. `docker push maxichattas/mywebapi:latest` : sube la imagen a Docker Hub.
4. `docker pull maxichattas/mywebapi:latest` : se verifica que la imagen se haya subido correctamente, al intentar descargarla desde Docker Hub.

![Imagen Paso 7e](Imágenes/Paso%207e.jpg)

Se adjunta además un link directo a la imagen subida en la plataforma:
[My Web API](https://hub.docker.com/r/maxichattas/mywebapi)

## Paso 8
Se crea un nuevo contenedor con la imagen mywebapi a través del comando `docker run --name myapi -d mywebapi`. Además, usando el comando `docker ps` se observa que el contenedor expone los puertos 80, 443 y 5254.

![Imagen Paso 8a](Imágenes/Paso%208a.jpg)

Por más que los puertos están expuestos, no es posible acceder desde el navegador, ya que los puertos expuestos no están mapeados a los puertos del dispositivo. Por este motivo, se elimina el contenedor previamente creado y lo creamos nuevamente configurando correctamente los puertos mediante el comando: `docker run --name myapi -d -p 80:80 mywebapi`

![Imagen Paso 8b](Imágenes/Paso%208b.jpg)

De este modo, ingresando a [http://localhost/WeatherForecast](http://localhost/WeatherForecast) podemos ver los datos resultantes de la API.

![Imagen Paso 8c](Imágenes/Paso%208c.jpg)

## Paso 9
Luego de modificar el Dockerfile de la siguiente manera:
```
# ENTRYPOINT ["dotnet", "SimpleWebAPI.dll"]
CMD ["/bin/bash"]
```

Se reconstruye la imagen mediante el comando: `docker build -t mywebapi .`

![Imagen Paso 9a](Imágenes/Paso%209a.jpg)

De este modo, al iniciar el contenedor, la API no se ejecuta automáticamente, por lo que es necesario correr el comando `dotnet SimpleWebAPI.dll` en la consola del contenedor.

![Imagen Paso 9b](Imágenes/Paso%209b.jpg)

Finalmente, salimos del contenedor ejecutando `^C` para detener la API y se ejecuta el comando `exit`.

![Imagen Paso 9c](Imágenes/Paso%209c.jpg)

## Paso 10
A continuación, para montar un volúmen dentro del contenedor, se selecciona el directorio local con path:
```
/Users/maxichattas/Documents/UCC/4º Año/Ingeniería de Software III/Práctico/Prácticos/Container Volume
```

Se muestra a continuación cómo se montó el volúmen en el contenedor, y los comandos ejecutados para crear un archivo en este directorio desde la consola del contenedor.

![Imagen Paso 10a](Imágenes/Paso%2010a.jpg)

Como se puede ver, el archivo "hola.txt" fue creado tanto en el contenedor, como en el directorio local del dispositivo.
- Desde el contenedor:

![Imagen Paso 10b (Container)](Imágenes/Paso%2010b%20(Container).jpg)

- Desde el dispositivo:

![Imagen Paso 10b (Host)](Imágenes/Paso%2010b%20(Host).jpg)

Nota: la diferencia de horario entre la creación de ambos archivos se debe a que el contenedor tiene un huso horario diferente al dispositivo host.

## Paso 11
En primer lugar, se creó el contenedor con la imagen de Postgres y se montó un volúmen en una carpeta ".postgres" dentro de un directorio local del dispositivo.

![Imagen Paso 11a](Imágenes/Paso%2011a.jpg)

Luego, se ingresó al contenedor (en modo interactivo con el modificador -it) y operamos dentro de PostgreSQL, donde se creó una nueva tabla, se insertó una tupla en dicha tabla y se mostró la misma. Finalmente, se cerró la sesión con Postgres y salimos del contenedor.

![Imagen Paso 11b](Imágenes/Paso%2011b.jpg)

- Mediante el comando `docker run` el contenedor fue creado y quedó en estado "Running".
- Mediante el comando `docker exec`, utilizando el modificador -it se obtuvo acceso a la shell dentro del contenedor.

Para poder visualizar los datos de forma más ordenada y clara, nos conectamos a la instancia de Postgres utilizando la IDE DBeaver. Se puede ver a continuación que desde la misma insertamos una nueva fila en la tabla_a con el texto "Hola desde DBeaver!".

![Imagen Paso 11c](Imágenes/Paso%2011c.jpg)

Verificamos desde la consola del contenedor que la fila se insertó correctamente.

![Imagen Paso 11d](Imágenes/Paso%2011d.jpg)

## Paso 12
A continuación, se realizará el mismo proceso que en el paso anterior, pero esta vez utilizando el motor de base de datos SQL Server.

En primer lugar, se creó un directorio en el dispositivo host en donde luego se montará el volúmen para permitir la persistencia de los datos, incluso si se elimina el contenedor. Este directorio se encuentra en el path:
```
/Users/maxichattas/Documents/UCC/4º Año/Ingeniería de Software III/Práctico/Prácticos/SQL Server
```

Luego, mediante el comando `docker pull mcr.microsoft.com/mssql/server:2022-latest` descargamos la imagen de SQL Server desde Docker Hub.

![Imagen Paso 12a](Imágenes/Paso%2012a.jpg)

Mediante el comando:
`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Mypassword123' -p 1433:1433 --name sqlserver -v "/Users/maxichattas/Documents/UCC/4º Año/Ingeniería de Software III/Práctico/Prácticos/SQL Server:/var/opt/mssql" -d mcr.microsoft.com/mssql/server:2022-latest`
se inicia la ejecución del contenedor que tiene la imagen de SQL Server. A continuación se explican los modificadores y variables utilizadas:
- `-e 'ACCEPT_EULA=Y'` : acepta los términos y condiciones de la licencia de SQL Server.
- `SA_PASSWORD=Mypassword123` : establece la contraseña del SA (System Administrator).
- `-p 1433:1433` : Expone y mapea el puerto 1433 del contenedor al puerto 1433 del dispositivo host.
- `--name sqlserver` : establece el nombre del contenedor.
- `-v "/Users/maxichattas/Documents/UCC/4º Año/Ingeniería de Software III/Práctico/Prácticos/SQL Server:/var/opt/mssql"` : monta un volúmen en los directorios especificados.
- `-d` : corre el contenedor en modo "detached" (no se ingresa al shell del contenedor).
- ` mcr.microsoft.com/mssql/server:2022-latest` : imagen utilizada.

![Imagen Paso 12b](Imágenes/Paso%2012b.jpg)

Se utilizó el comando:
`docker exec -it sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P 'Mypassword123' -C`
para acceder a la consola de SQL Server y poder operar con los elementos del motor.

Luego de ingresar, se creó una nueva base de datos (test), una tabla dentro de la base de datos (tabla_a) y se insertó un elemento en ella, que luego fue mostado mediante un SELECT.

![Imagen Paso 12c](Imágenes/Paso%2012c.jpg)

A su vez, esta instancia de SQL Server fue accedida mediante la IDE DBeaver, como se muestra a continuación.

![Imagen Paso 12d](Imágenes/Paso%2012d.jpg)