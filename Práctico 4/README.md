# Trabajo Práctico 4

## Paso 1
Se verifica el acceso concedido a los pipelines probando correr uno de ellos.

![Imagen Paso 1a](Imágenes/Paso%201a.jpg)

A su vez, se adjunta el mail recibido por parte de Azure DevOps confirmando el acceso concedido.

![Imagen Paso 1b](Imágenes/Paso%201b.jpg)

## Paso 2
Se agrega en el pipeline una tarea de publish y se hace un commit de los cambios. Esto automáticamente corre el pipeline nuevamente.

![Imagen Paso 2](Imágenes/Paso%202.jpg)

## Paso 3
La tarea de Publish es fundamental dentro de los pipelines, ya que permiten que los archivos generados durante la ejecución del pipeline, como ejecutables, dll y demás, se almacenen y se encuentren disponibles. Estos luego se pueden utilizar para ejecutarlos o en etapas posteriores, como test y deploy.

## Paso 4
### Descarga de Drop
Debido a la publish task agregada en el paso anterior, se genera un drop que puede ser descargado. Esto nos permite correr la aplicación localmente en el dispositivo.

En primer lugar, descargamos el drop con los archivos generados.

![Imagen Paso 4a](Imágenes/Paso%204a.jpg)

### Cambio de Versión de .NET
Al intentar correr la aplicación localmente, el proceso se interrumpía debido a un error que hacía referencia a la versión de dotnet 7.0. Por este motivo, se modificó el archivo `SimpleWebApi.runtimeconfig.json` para que soporte la versión de dotnet instalada localmente en el dispositivo, como se muestra a continuación.

![Imagen Paso 4b](Imágenes/Paso%204b.jpg)

## Ejecución de la aplicación localmente
Una vez realizado el cambio de versión, mediante el comando `dotnet SimpleWebApi.dll` fue posible comenzar la ejecución de la aplicación en el puerto 5000 de localhost.

![Imagen Paso 4c](Imágenes/Paso%204c.jpg)

Su resultado se pudo ver en el navegador del dispositivo, ingresando a [localhost:5000/weatherforecast](http://localhost:5000/weatherforecast) como se muestra a continuación.

![Imagen Paso 4d](Imágenes/Paso%204d.jpg)

## Paso 5
Mediante la configuración de la organización de Azure DevOps, se habilita el editor clásico de pipeline.

![Imagen Paso 5](Imágenes/Paso%205.jpg)

Los pipelines de tipo yaml forman parte del código fuente y es versionable, a diferencia de los pipelines clásicos, que son más visuales pero más sencillos y menos técnicos. Conceptualmente son idénticos, pero los pipelines clásicos contienen una interfaz gráfica más amigable, y la diferencia fundamental es que no forma parte del repositorio (código fuente).

## Paso 6
### Crear pipeline clásico
Mediante el editor de Azure DevOps, se creó un pipeline de tipo clásico utilizando el template ASP .NET Core.

![Imagen Paso 6a](Imágenes/Paso%206a.jpg)

## Descarga del Drop
Una vez finalizada la ejecución del pipeline exitósamente, se encuentran disponibles para descargar localmente en el dispositivo los archivos generados.

![Imagen Paso 6b](Imágenes/Paso%206b.jpg)

## Ejecución de la aplicación localmente
Luego de realizar las modificaciones correspondientes en el archivo `SimpleWebApi.runtimeconfig.json` al igual que en pasos anteriores, es posible correr la aplicación de forma local.

![Imagen Paso 6c](Imágenes/Paso%206c.jpg)

Se observa la ejecución de la aplicación desde el navegador del dispositivo.

![Imagen Paso 6d](Imágenes/Paso%206d.jpg)

## Paso 7
### CI en Classic Editor
Se habilita CI (continuous integration) en el pipeline construido utilizando el classic editor, opción que por defecto se encuentra deshabilitada.

![Imagen Paso 7a](Imágenes/Paso%207a.jpg)

### CI en YAML
En el pipeline construido utilizando YAML, CI (continuous integration) por defecto se encuentra habilitado, como se muestra a continuación.

![Imagen Paso 7b](Imágenes/Paso%207b.jpg)

### Ejecución automática de pipelines
A partir de las opciones habilitadas en los pasos anteriores, al hacer un commit en la main branch ambos pipelines se ejecutan de manera automática, construyendo la nueva versión del producto.

Para probar que CI haya sido configurado correctamente, en primer lugar se realiza un commit sobre la main branch.

![Imagen Paso 7c](Imágenes/Paso%207c.jpg)

Este commit automáticamente provocó que los pipelines configurados anteriormente comiencen a ejecutarse, como se muestra a continuación.

![Imagen Paso 7d](Imágenes/Paso%207d.jpg)

Una vez finalizada la ejecución, se tiene la nueva versión del producto modificado.

![Imagen Paso 7e](Imágenes/Paso%207e.jpg)

## Paso 8
Los agentes son software encargados de correr pipelines, que a su vez corren en una determinada máquina.
En Azure DevOps existen dos tipos de agentes para los pipelines: aquellos brindados por Microsoft y aquellos que corren localmente. La diferencia entre los dos tipos, es que en el primer caso, se utilizan los recursos de Microsoft, mientras que en el segundo tipo se utilizan recursos propios.

Los self hosted agents son más difíciles de configurar correctamente, pero se utilizan en caso de que el proyecto sea muy grande con muchas horas de compilación, lo cual implica un elevado costo para correrlo utilizando los recursos de Microsoft. Este tipo de agentes también se utilizan debido a que las máquinas de Microsoft ya tienen ciertos componentes preinstalados, y estos no se pueden modificar, por lo que son menos configurables. Entonces, se utilizan self hosted cuando se hace uso de un componente que no está público en las máquinas de Microsoft. A su vez, utilizar los Microsoft Hosted Agents implica que toda la información del proyecto se debe enviar a los servidores de Microsoft para realizar los build y deploy correspondientes, por lo que si se tienen estrictas políticas de seguridad y privacidad, es recomendable utilizar Self Hosted Agents.

Por otro lado, la ventaja de los Microsoft Hosted Agents es que son más sencillos de configurar e implementar debido a las plantillas ofrecidas por Microsoft, además de que no se requieren recursos propios, ya que se utilizan máquinas virtuales dentro del servidor de Microsoft.

## Paso 9
### Creación de Agent Pool
Dentro de las configuraciones de la organización, se crea un nuevo "Agent Pool" de tipo Self-Hosted. El nombre escogido para el mismo fue "New Self-Hosted Agent Pool"

![Imagen Paso 9a](Imágenes/Paso%209a.jpg)

### Creación de Agente Self-Hosted
Luego, dentro del Agent Pool, se creó un Self-Hosted Agent. A continuación se muestran sus características.

![Imagen Paso 9b](Imágenes/Paso%209b.jpg)

## Paso 10
### Descarga de Agente
Este agente luego se descarga en el dispositivo localmente mediante los comandos que se muestran a continuación.

![Imagen Paso 10a](Imágenes/Paso%2010a.jpg)

De este modo, se descomprime el archivo descargado en un nuevo directorio propio del agente.

![Imagen Paso 10b](Imágenes/Paso%2010b.jpg)

## Configuración de Agente
Para configurar el agente descargado, en primer lugar es necesario crear un PAT (Personal Access Token). En este caso, al mismo se le otorgaron todos los permisos posibles.

![Imagen Paso 10c](Imágenes/Paso%2010c.jpg)

Luego, en el directorio del agente creado anteriormente, se ejecuta el comando `config.sh` para iniciar la configuración del agente Self-Hosted. Esta configuración, requiere otorgar ciertos permisos por cuestiones de seguridad, pero después de varios intentos, es posible realizar el proceso. En este paso, se requiere el PAT creado anteriormente.

![Imagen Paso 10d](Imágenes/Paso%2010d.jpg)

## Ejecución de Agente
Una vez finalizada la configuración, mediante el comando `./run.sh` dentro del mismo directorio, comienza la ejecución del agente. De este modo, el agente comienza a estar disponible para recibir "jobs".

![Imagen Paso 10e](Imágenes/Paso%2010e.jpg)

## Paso 11
### Creación de Pipeline
Una vez configurado el agente en el dispositivo local, se crea un pipeline a partir del proyecto Sample02 que lo utilice. Para ello, se parte desde el Classic Editor y se lo configura seleccionando el Agent Pool anteriormente definido.

![Imagen Paso 11a](Imágenes/Paso%2011a.jpg)

## Ejecución del Pipeline
Luego de crear el pipeline, se ejecuta el mismo en el agente local. A continuación se muestra desde la terminal que el resultado de la ejecución fue exitoso.

![Imagen Paso 11b](Imágenes/Paso%2011b.jpg)

Esto también se puede ver desde Azure DevOps, en los resultados de los jobs ejecutados.

![Imagen Paso 11c](Imágenes/Paso%2011c.jpg)

## Paso 12
Una vez ejecutado el pipeline, los archivos resultantes se encuentran dentro del directorio:
```
/Users/maxichattas/Downloads/myagent/_work/4/a
```
El archivo se encontraba en formato .zip, por lo que fue necesario descomprimirlo.

![Imagen Paso 12a](Imágenes/Paso%2012a.jpg)

Una vez descomprimido el archivo, mediante el comando `dotnet SimpleWebAPI.dll` se corre la aplicación de manera local.

![Imagen Paso 12b](Imágenes/Paso%2012b.jpg)

Su resultado se pudo ver en el navegador del dispositivo, ingresando a [localhost:5000/weatherforecast](http://localhost:5000/weatherforecast) como se muestra a continuación.

![Imagen Paso 12c](Imágenes/Paso%2012c.jpg)

## Paso 13
Para el siguiente desafío, fue creado un nuevo proyecto en Azure DevOps denominado `Sample03 (Angular)`, que utiliza un repositorio con una aplicación de Angular.

[https://github.com/ingsoft3ucc/angular-demo-project](https://github.com/ingsoft3ucc/angular-demo-project)

![Imagen Paso 13](Imágenes/Paso%2013.jpg)

## Paso 14
Luego, se creó un build pipeline para el proyecto de Angular anteriormente clonado. Para esto, se utilizó un archivo de tipo YAML.

![Imagen Paso 14a](Imágenes/Paso%2014a.jpg)

El archivo fue obtenido de una contribución a una publicación de StackOverFlow que se adjunta a continuación.
[https://stackoverflow.com/questions/76985518/build-and-deployment-of-nodejs-with-angular-using-asude-pipeline-and-appservice](https://stackoverflow.com/questions/76985518/build-and-deployment-of-nodejs-with-angular-using-asude-pipeline-and-appservice)

A este archivo fue necesario realizarle algunas modificaciones, pero luego de estas, el pipeline fue ejecutado de manera exitosa.

![Imagen Paso 14b](Imágenes/Paso%2014b.jpg)

## Paso 15
Anteriormente se mostró que el pipeline contiene la siguiente porción de código:
```
trigger:
- main
```
Esto significa que CI ya se encuentra implementado, y por ende el pipeline será ejecutado de manera automática cuando se realice un commit en la main branch.

## Paso 16
Para demostrar la correcta implementación de CI en el pipeline, se realiza un cambio en el encabezado de la pantalla principal de la aplicación para hacer un commit en la rama main.

![Imagen Paso 16a](Imágenes/Paso%2016a.jpg)

Este cambio desencadenó la ejecución automática del pipeline anteriormente definido, el cual finalizó exitosamente.

![Imagen Paso 16b](Imágenes/Paso%2016b.jpg)

## Paso 17
Luego de la exitosa ejecución del pipeline, se descargaron los artefactos resultantes. Estos fueron descomprimidos y finalmente se inició el servidor en el puerto :8080.

![Imagen Paso 17](Imágenes/Paso%2017.jpg)

## Paso 18
### Original
Se muestra a continuación la página original, resultado de la ejecución del pipeline en el [Paso 14](#paso-14).

![Imagen Paso 18a](Imágenes/Paso%2018a.jpg)

### Modificado
Se muestra a continuación página con su encabezado modificado, resultado de la ejecución del pipeline en el [Paso 16](#paso-16)

![Imagen Paso 18b](Imágenes/Paso%2018b.jpg)