# Trabajo Práctico 6

## Prerequisitos
Se muestra a continuación que todas las dependencias requeridas se encuentran instaladas en el dispositivo.

![Imagen Paso 0](Paso%200.jpg)

## Paso 1
La base de datos Microsoft SQL Server para utilizar en este proyecto fue montada en un contenedor de Docker. En primer lugar, se realiza un pull de la imagen, para poder utilizarla en un contenedor.

![Imagen Paso 1a](Paso%201a.jpg)

Luego, se crea el contenedor definiendo el usuario y contraseña y exponiendo el puerto 1433 para que sea accesible por la aplicación.

![Imagen Paso 1b](Paso%201b.jpg)

Luego, se corre el script proporcionado que crea una tabla y carga los datos iniciales dentro de la base.

![Imagen Paso 1c](Paso%201c.jpg)

Se muestra que los datos fueron insertados correctamente.

![Imagen Paso 1d](Paso%201d.jpg)

De este modo, la base de datos se encuentra correctamente inicializada y lista para ser utiliada por la aplicación.

## Paso 2
La aplicación a utilizar se encuentra en un repositorio de GitHub, por lo que se realiza un clone del mismo en el dispositivo de manera local.
Para esto, se utilizó el comando:

`git clone https://github.com/ingsoft3ucc/Angular_WebAPINetCore8_CRUD_Sample.git`

![Imagen Paso 2a](Paso%202a.jpg)

Se corre la aplicación de manera local para verificar que funcione correctamente.

![Imagen Paso 2b](Paso%202b.jpg)

Se prueba que la aplicación esté funcionando en: [http://localhost:7150/swagger/index.html](http://localhost:7150/swagger/index.html)

A continuación se puede ver que los endpoints están disponibles.

![Imagen Paso 2c](Paso%202c.jpg)

Se prueba además la conexión con la base de datos a través de uno de los controladores. Como se puede observar, se reciben los datos insertados anteriormente.

![Imagen Paso 2d](Paso%202d.jpg)

Luego, se inicializa el proyecto de Angular. Mediante el comando `npm install` se instalan las dependencias, y mediante el comando `ng serve -o` se levanta el proyecto.

![Imagen Paso 2e](Paso%202e.jpg)

Se observa que el mismo ya se encuentra escuchando en el puerto 4200, por lo que se navega a [http://localhost:4200](http://localhost:4200) para verificarlo.

![Imagen Paso 2f](Paso%202f.jpg)

## Paso 3
Se crea un nuevo proyecto de pruebas unitarias mediante el comando:
`dotnet new xunit -n EmployeeCrudApi.Tests`

![Imagen Paso 3a](Paso%203a.jpg)

Luego, se instalan las dependencias mediante los comandos:
```bash
dotnet add package Moq
dotnet add package xunit
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

Se muestra que las dependencias fueron instaladas correctamente, listándolas mediante el comando `dotnet list package`
![Imagen Paso 3b](Paso%203b.jpg)

Se modifica el contenido del archivo "UnitTest1.cs".

![Imagen Paso 3c](Paso%203c.jpg)

Se cambia el nombre del archivo mediante el comando
```bash
mv UnitTest1.cs EmployeeControllerUnitTests.cs 
```

![Imagen Paso 3d](Paso%203d.jpg)

Se modifica el contenido del archivo "EmployeeCrudApi.Tests.csproj".

![Imagen Paso 3e](Paso%203e.jpg)

Se construye la aplicación mediante el comando:
```bash
dotnet build
```

Y se ejecutan las pruebas unitarias mediante el comando:
```bash
dotnet test
```

![Imagen Paso 3f](Paso%203f.jpg)

Se observa que las pruebas unitarias se ejecutaron con éxito.

Luego, cambiamos la cadena de conexión para que falle el acceso a la base de datos. Esto se utiliza para garantizar que las pruebas unitarias no tengan dependencias con la misma. Por esto, se cambia la contraseña a "WRONGPASSWORD".

![Imagen paso 3g](Paso%203g.jpg)

Se ejecuta la aplicación nuevamente.

![Imagen Paso 3h](Paso%203h.jpg)

Se verifica que la aplicación no tenga acceso a la base de datos, ya que se muestra a continuación que el resultado es un error.

![Imagen Paso 3i](Paso%203i.jpg)

Se muestra que, por más que la aplicación no tenga acceso a la base de datos, las pruebas unitarias continúan corriendo correctamente.

![Imagen Paso 3j](Paso%203j.jpg)

Finalmente, se restablece la cadena de conexión a la correcta, verificando que la aplicación funciona correctamente de nuevo.

![Imagen Paso 3k](Paso%203k.jpg)

## Paso 4
Se edita el contenido del archivo "app.component.spec.ts" por el código provisto.

![Imagen Paso 4a](Paso%204a.jpg)

Se crea el archivo "employee.service.spec.ts" con el código provisto.

![Imagen Paso 4b](Paso%204b.jpg)

Se edita el contenido del archivo "employee.component.spec.ts" por el código provisto.

![Imagen Paso 4c](Paso%204c.jpg)

Se edita el contenido del archivo "addemployee.component.spec.ts" por el código provisto.

![Imagen Paso 4d](Paso%204d.jpg)

Mediante el comando
```bash
ng test
```
se ejecutan las pruebas anteriormente creadas.

![Imagen Paso 4e](Paso%204e.jpg)

Se muestra que las 4 pruebas finalizaron de manera exitosa.

![Imagen Paso 4f](Paso%204f.jpg)

Se muestra que la API no se encuentra corriendo al momento de ejecutar las pruebas, demostrando que las mismas son realmente pruebas unitarias que no requieren dependencias externas para funcionar.

![Imagen Paso 4g](Paso%204g.jpg)

## Paso 5
Se instala la dependencia karma-junit-reporter mediante el comando:
```bash
npm install karma-junit-reporter --save-dev
```

![Imagen Paso 5a](Paso%205a.jpg)

Se crea el archivo "karma.conf.js"

![Imagen Paso 5b](Paso%205b.jpg)

Se ejecutan las pruebas nuevamente mediante el comando:
```bash
ng test --karma-config=karma.conf.js --watch=false --browsers ChromeHeadless
```

![Imagen Paso 5c](Paso%205c.jpg)

Se muestra que esto resultó en la creación de un archivo "test-results.xml" dentro de un directorio "test-results" en el directorio raíz del proyecto de Angular.

![Imagen Paso 5d](Paso%205d.jpg)