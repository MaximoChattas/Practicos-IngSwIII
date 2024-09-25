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

## Paso 6
### Validaciones en el código
Dentro del proyecto, se realizan las siguientes modificaciones:
1. Verificar que el nombre no contenga números, ya que no es común en los nombres de empleados.
2. Almacenar el nombre en la BD siempre con la primera letra de los nombres en Mayuscula y todo el apellido en Mayusculas.
3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.
4. Asegurar que cada parte del nombre (separada por espacios) tenga más de un carácter.
5. La longitud máxima del nombre y apellido del empleado debe ser de 100 caracteres.
6. Evitar que se ingresen caracteres repetidos de forma excesiva.

En primer lugar, se implementan las funciones que realizan estas verificaciones. Las mismas reciben como parámetro el nombre del empleado a verificar, y retornan un booleano, excepto por la función de formateo de nombre que devuelve un string con el nombre escrito correctamente. A continuación se adjunta el código de estas funciones.

```c#
// 1: Chequeo de ausencia de números
private bool CheckForNumbersInName(string name)
{
    return name.Any(char.IsDigit);
}

// 2: Formatear el nombre y apellido
private string FormatName(string name)
{
    var parts = name.Split(' ');
    for (int i = 0; i < parts.Length; i++)
    {
        if (i == parts.Length - 1)
        {
            // El apellido en mayúsculas (última palabra)
            parts[i] = parts[i].ToUpper();
        }
        else
        {
            // Nombres con la primera letra mayúscula
            parts[i] = char.ToUpper(parts[i][0]) + parts[i].Substring(1).ToLower();
        }
    }
    return string.Join(" ", parts);
}

// 3: Chequeo de nombre duplicado
private bool IsNameDuplicate(string name)
{
    // Se realiza la comparación en minúsculas para garantizar la ausencia de duplicados
    return _context.Employees.Any(e => e.Name.ToLower() == name.ToLower());
}

// 4: Chequeo que todas las partes del nombre tengan más de 1 caracter
private bool AreNamePartsValid(string name)
{
    var parts = name.Split(' ');
    return parts.All(p => p.Length > 1);
}

// 5: Chequeo máxima longitud 100 caracteres
private bool IsNameLengthValid(string name)
{
    return name.Length <= 100;
}

// 6: Chequeo de repetición excesiva de caracteres
private bool NameHasExcessiveRepeatedCharacters(string name)
{
    int counter = 1;
    for (int i = 1; i < name.Length; i++)
    {
        if (name[i] == name[i-1])
        {
            counter++;
            if (counter > 2)
                return true;
        }
        else
        {
            counter = 1;
        }
    }

    return false;
}
```

Estas funciones se utilizan para realizar las verificaciones durante la creación y actualización de los empleados. Si alguna de las verificaciones falla, se retorna un BadRequest (Error 400) con una descripción de la verificación fallida.

Se muestra cómo se realizan las verificaciones durante la creación del empleado.

![Imagen Paso 6a](Paso%206a.jpg)

Se muestra cómo se realizan las verificaciones durante la actualización del empleado.

![Imagen Paso 6b](Paso%206b.jpg)

### Pruebas manuales

A continuación se prueban de forma manual algunas de las modificaciones implementadas para visualizar su correcto funcionamiento.

Se prueba que el nombre del empleado no contenga números.

![Imagen Paso 6c](Paso%206c.jpg)

Se prueba que cada parte del nombre deba contener más de 1 caracter.

![Imagen Paso 6d](Paso%206d.jpg)

Se actualiza el empleado con ID 1.

![Imagen Paso 6e](Paso%206e.jpg)

Y se verifica que su nombre haya sido formateado adecuadamente.

![Imagen Paso 6f](Paso%206f.jpg)

### Escritura de casos de prueba
A continuación, se escriben casos de prueba que verifican el correcto funcionamiento de las validaciones previamente definidas.

#### Creación de empleado
1. Verificar que el nombre no contenga números, ya que no es común en los nombres de empleados.

```c#
[Fact]
public async Task Create_ReturnsBadRequest_NameContainsNumbers()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var newEmployee = new Employee { Id = 4, Name = "New Employee 1" };

    // Act
    var result = await controller.Create(newEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

2. Almacenar el nombre en la BD siempre con la primera letra de los nombres en Mayuscula y todo el apellido en Mayusculas.

```c#
[Fact]
public async Task Create_AddsEmployee()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var newEmployee = new Employee { Id = 3, Name = "New Employee" };

    // Act
    await controller.Create(newEmployee);

    // Assert
    var employee = await context.Employees.FindAsync(3);
    Assert.NotNull(employee);
    Assert.Equal("New EMPLOYEE", employee.Name);
}
```

3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.

```c#
[Fact]
public async Task Create_ReturnsBadRequest_NameIsDuplicate()
{
    // Arrange
    var context = GetInMemoryDbContext();
    context.Employees.Add(new Employee { Id = 1, Name = "John Doe"} );
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var duplicateEmployee = new Employee { Id = 1, Name = "john doe" };

    // Act
    var result = await controller.Create(duplicateEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

4. Asegurar que cada parte del nombre (separada por espacios) tenga más de un carácter.

```c#
[Fact]
public async Task Create_ReturnsBadRequest_NamePartLength1()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var employee = new Employee { Id = 1, Name = "John D" };

    // Act
    var result = await controller.Create(employee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

5. La longitud máxima del nombre y apellido del empleado debe ser de 100 caracteres.

```c#
[Fact]
public async Task Create_ReturnsBadRequest_NameLengthOver100()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var longName = new string('A', 120);

    var employee = new Employee { Id = 1, Name = longName };

    // Act
    var result = await controller.Create(employee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

6. Evitar que se ingresen caracteres repetidos de forma excesiva.

```c#
[Fact]
public async Task Create_ReturnsBadRequest_NameContainsRepeatedCharacters()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var newEmployee = new Employee { Id = 4, Name = "New Employeeeee" };

    // Act
    var result = await controller.Create(newEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

#### Actualización de empleado
1. Verificar que el nombre no contenga números, ya que no es común en los nombres de empleados.

```c#
[Fact]
public async Task Update_UpdatesEmployee_ReturnsBadRequest_NameContainsNumbers()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var existingEmployee = new Employee { Id = 1, Name = "Old Name" };
    context.Employees.Add(existingEmployee);
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var updatedEmployee = new Employee { Id = 1, Name = "Updated Name1" };

    // Act
    var result = await controller.Update(updatedEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

2. Almacenar el nombre en la BD siempre con la primera letra de los nombres en Mayuscula y todo el apellido en Mayusculas.

```c#
[Fact]
public async Task Update_UpdatesEmployee()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var existingEmployee = new Employee { Id = 1, Name = "Old Name" };
    context.Employees.Add(existingEmployee);
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var updatedEmployee = new Employee { Id = 1, Name = "Updated Name" };

    // Act
    await controller.Update(updatedEmployee);

    // Assert
    var employee = await context.Employees.FindAsync(1);
    Assert.NotNull(employee);
    Assert.Equal("Updated NAME", employee.Name);
}
```

3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.

```c#
[Fact]
public async Task Update_UpdatesEmployee_ReturnsBadRequest_NameIsDuplicate()
{
    // Arrange
    var context = GetInMemoryDbContext();
    context.Employees.AddRange(
        new Employee { Id = 1, Name = "John Doe" },
        new Employee { Id = 2, Name = "Jane Doe" }
    );
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var updatedEmployee = new Employee { Id = 1, Name = "jane doe" };

    // Act
    var result = await controller.Update(updatedEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

4. Asegurar que cada parte del nombre (separada por espacios) tenga más de un carácter.

```c#
[Fact]
public async Task Update_UpdatesEmployee_ReturnsBadRequest_NamePartLength1()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var existingEmployee = new Employee { Id = 1, Name = "Old Name" };
    context.Employees.Add(existingEmployee);
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var updatedEmployee = new Employee { Id = 1, Name = "New n name" };

    // Act
    var result = await controller.Update(updatedEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

5. La longitud máxima del nombre y apellido del empleado debe ser de 100 caracteres.

```c#
[Fact]
public async Task Update_UpdatesEmployee_ReturnsBadRequest_NameLengthOver100()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var existingEmployee = new Employee { Id = 1, Name = "Old Name" };
    context.Employees.Add(existingEmployee);
    context.SaveChanges();

    var controller = new EmployeeController(context);

    var longName = new string('A', 120);

    var updatedEmployee = new Employee { Id = 1, Name = longName };

    // Act
    var result = await controller.Update(updatedEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

6. Evitar que se ingresen caracteres repetidos de forma excesiva.

```c#
[Fact]
public async Task Updates_ReturnsBadRequest_NameContainsRepeatedCharacters()
{
    // Arrange
    var context = GetInMemoryDbContext();
    var controller = new EmployeeController(context);

    var newEmployee = new Employee { Id = 4, Name = "New Employeeeee" };

    // Act
    var result = await controller.Update(newEmployee);

    // Assert
    Assert.IsType<BadRequestObjectResult>(result);
}
```

### Ejecución de pruebas
Se ejecutan las pruebas escritas (junto con las anteriormente definidas en el proyecto) y se observa que todas ellas finalizan con resultado exitoso.

![Imagen Paso 6g](Paso%206g.jpg)

## Paso 7
### Instalación y configuración de dependencias
Para mostrar las validaciones mediante Toast, es necesario instalar y modificar archivos y dependencias. El proceso realizado a continuación fue tomado de la siguiente fuente:

[https://www.npmjs.com/package/ngx-toastr](https://www.npmjs.com/package/ngx-toastr)

En primer lugar, se instalan las dependencias mediante los comandos:
```bash
npm install ngx-toastr --save
npm install @angular/animations --save
```

Luego, se agrega el archivo .css correspondiente a los toast dentro del archivo "angular.json".

![Imagen Paso 7a](Paso%207a.jpg)

Se importan las dependencias instaladas en el archivo "app.config.ts".

![Imagen Paso 7b](Paso%207b.jpg)

A su vez, se importó el archivo css de Toastr en el archivo "styles.css" del proyecto.

![Imagen Paso 7c](Paso%207c.jpg)

## Validaciones en el código
Dentro del proyecto de Angular, se implementaron las mismas validaciones que en la API, permitiendo que este proceso sea más eficiente debido a que se reduce la cantidad de llamadas a la misma (en caso de error, la información nunca llega a la API). Para esto, se definió una función que determina si el nombre del empleado es válido o no, de acuerdo con las políticas definidas y muestran el error utilizando Toastr.

```typescript
// Validaciones a realizar sobre el nombre del empleado
private isValidEmployeeName(name: string): boolean {

    // 1: Chequeo que el nombre no contenga números
    if (/\d/.test(name)) {
      this.toastr.error('El nombre del empleado no debe contener números.', 'Error de validación');
      return false;
    }

    // 4: Se verifica que todas las partes del nombre tengan más de 1 caracter
    const nameParts = name.split(' ');
    for (let part of nameParts) {
      if (part.length <= 1) {
        this.toastr.error('Los nombres y apellidos del empleado debe tener una longitud mayor a 1 letra.', 'Error de validación');
        return false;
      }
    }

    // 5: Se verifica máxima longitud de nombre 100 caracteres
    if (name.length > 100) {
      this.toastr.error('El nombre del empleado no puede contener más de 100 letras.', 'Error de validación');
      return false;
    }

     // 6: Se verifica la ausencia de caracteres repetidos excesivamente
    if (this.hasExcessiveRepeatedCharacters(name)) {
      this.toastr.error('El nombre del empleado no debe contener caracteres repetidos de manera excesiva.', 'Error de validación');
      return false;
    }

    return true;
  }
```

Además, se creó una función que realiza el formateo del nombre del modo definido: apellido en mayúsculas en su totalidad y nombres con la primera letra mayúscula, y el resto en minúscula.

```typescript
// 2: Formato de nombre
private formatName(name: string): string {
const nameParts = name.split(' ');
const formattedParts = nameParts.map((part, index) => {
    return index === nameParts.length - 1 // Apellido (ultima palabra)
    ? part.toUpperCase()
    : part.charAt(0).toUpperCase() + part.slice(1).toLowerCase(); // Nombres
});
return formattedParts.join(' ');
}
```

Y una función que verifica que el nombre no contenga un mismo caracter repetido más de 2 veces (utilizada en isValidEmployeeName).

```typescript
// 6: Chequeo de repetición excesiva de caracteres
private hasExcessiveRepeatedCharacters(name: string): boolean {
  let repetitionCount = 1;

  for (let i = 1; i < name.length; i++) {
    if (name[i] === name[i - 1]) {
      repetitionCount++;
      if (repetitionCount > 2) {
        return true;
      }
    } else {
      repetitionCount = 1;
    }
  }

  return false;
}
```

Luego, estas funciones son utilizadas durante la creación y actualización de los empleados, que a su vez son capaces de recibir y mostrar errores de la API utilizando el mismo formato.

#### Creación de empleado

```typescript
createEmployee(employee: Employee) {

    // Verificación de validez
    if (this.isValidEmployeeName(employee.name)) {

      // Formateo de nombre
      employee.name = this.formatName(employee.name);

      const httpOptions = {
        headers: new HttpHeaders({ 'Content-Type': 'application/json' }),
      };
      return this.http.post<Employee>(
        this.apiUrlEmployee + '/create',
        employee,
        httpOptions
      ).pipe(
        // Error de API
        catchError((error: HttpErrorResponse) => {
          const errorMessage = error.error;
          this.toastr.error(errorMessage, 'Error al crear empleado');
          return throwError(() => error);
        })
      );
    } else {
      return throwError(() => new Error('Employee name validation failed.'));
    }
  }
```

#### Actualización de empleado

```typescript
updateEmployee(employee: Employee): Observable<Employee> {

    // Verificación de validez
    if (this.isValidEmployeeName(employee.name)) {

      // Formateo de nombre
      employee.name = this.formatName(employee.name);
  
      const httpOptions = {
        headers: new HttpHeaders({ 'Content-Type': 'application/json' }),
      };
  
      return this.http.put<Employee>(
        `${this.apiUrlEmployee}/update`,
        employee,
        httpOptions
      ).pipe(
        // Error de API
        catchError((error: HttpErrorResponse) => {
          const errorMessage = error.error;
          this.toastr.error(errorMessage, 'Error al actualizar empleado');
          return throwError(() => error);
        })
      );
    } else {
      return throwError(() => new Error('Error de validación.'));
    }
  }
```

Nota: la validación

3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.

no fue tenida en cuenta para el frontend, debido a que requiere obtener un listado completo de los empleados actualmente registrados (utilizando la conexión a la API y la base de datos). Por este motivo, se implementó la lectura de errores provenientes de la API en las funciones de creación y actualización de empleados, con su correspondiente caso de prueba.

### Pruebas manuales
A continuación se prueban de forma manual algunas de las modificaciones implementadas para visualizar su correcto funcionamiento.

Se prueba que el nombre del empleado no contenga números.

![Imagen Paso 7d](Paso%207d.jpg)

Se prueba que cada parte del nombre deba contener más de 1 caracter.

![Imagen Paso 7e](Paso%207e.jpg)

Se actualiza uno de los empleados.

![Imagen Paso 7f](Paso%207f.jpg)

Y se verifica que su nombre haya sido formateado correctamente.

![Imagen Paso 7g](Paso%207g.jpg)

Se intenta crear un nuevo empleado con un nombre existente en el sistema.

![Imagen Paso 7h](Paso%207h.jpg)

### Escritura de casos de prueba
A continuación, se escriben casos de prueba que verifican el correcto funcionamiento de las validaciones previamente definidas.

#### Creación de empleado
1. Verificar que el nombre no contenga números, ya que no es común en los nombres de empleados.

```typescript
it('should fail to create employee when name contains numbers', () => {
    const invalidEmployee = new Employee (1, 'John 1', '');
    service.createEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no debe contener números.', 'Error de validación');

  });
```

2. Almacenar el nombre en la BD siempre con la primera letra de los nombres en Mayuscula y todo el apellido en Mayusculas.

```typescript
it('should format employee name when creating an employee', () => {
    const employee = new Employee(1, 'john doe', '');
  
    const formatNameSpy = spyOn<any>(service, 'formatName').and.callThrough(); 
  
    service.createEmployee(employee).subscribe((response) => {
      expect(employee.name).toEqual('John DOE');
    });
  
    const req = httpMock.expectOne(`${service.apiUrlEmployee}/create`);
    expect(req.request.method).toBe('POST');
  
    // Simulate a successful response from the backend
    req.flush(employee);
    
    expect(formatNameSpy).toHaveBeenCalledWith('john doe');
  });
```

3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.

Nota: en este caso se prueba que Angular reciba y muestre adecuadamente los errores ocurridos en la API.

```typescript
it('should handle API error on createEmployee', () => {
    const employee = new Employee(1, 'John Doe', '');
  
    // Simulate an error message from the backend
    const errorMessage = 'El empleado ya se encuentra registrado';
    const errorResponse = { status: 400, statusText: 'Bad Request' };
  
    // Call the service method
    service.createEmployee(employee).subscribe({
      next: () => fail('Expected an error, but got success response'),
      error: (error) => {
        expect(error.status).toBe(400);
        expect(error.statusText).toBe('Bad Request');
      }
    });
  
    // Expect an API call
    const req = httpMock.expectOne(`${service.apiUrlEmployee}/create`);
    expect(req.request.method).toBe('POST');
  
    // Simulate an API error response (passing the error as a string directly)
    req.flush(errorMessage, errorResponse);
  
    // Verify that the ToastrService error was called
    expect(toastr.error).toHaveBeenCalledWith(errorMessage, 'Error al crear empleado');
  });
```

4. Asegurar que cada parte del nombre (separada por espacios) tenga más de un carácter.

```typescript
it('should fail to create employee when a part of name is of length 1', () => {
    const invalidEmployee = new Employee (1, 'J D', '');
    service.createEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('Los nombres y apellidos del empleado debe tener una longitud mayor a 1 letra.', 'Error de validación');

  });
```

5. La longitud máxima del nombre y apellido del empleado debe ser de 100 caracteres.

```typescript
it('should fail to create employee when name is longer than 100', () => {
    const invalidEmployee = new Employee (1, 'A'.repeat(120), '');
    service.createEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no puede contener más de 100 letras.', 'Error de validación');

  });
```

6. Evitar que se ingresen caracteres repetidos de forma excesiva.

```typescript
it('should fail to create employee when name contains repeated characters', () => {
    const invalidEmployee = new Employee (1, 'Joooohn', '');
    service.createEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no debe contener caracteres repetidos de manera excesiva.', 'Error de validación');

  });
```

#### Actualización de empleado
1. Verificar que el nombre no contenga números, ya que no es común en los nombres de empleados.

```typescript
it('should fail to update employee when name contains numbers', () => {
    const invalidEmployee = new Employee (1, 'John 1', '');
    service.updateEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no debe contener números.', 'Error de validación');

  });
```

2. Almacenar el nombre en la BD siempre con la primera letra de los nombres en Mayuscula y todo el apellido en Mayusculas.

```typescript
it('should format employee name when updating an employee', () => {
    const employee = new Employee(1, 'john doe', '');
  
    const formatNameSpy = spyOn<any>(service, 'formatName').and.callThrough(); 
  
    service.updateEmployee(employee).subscribe((response) => {
      expect(employee.name).toEqual('John DOE');
    });
  
    const req = httpMock.expectOne(`${service.apiUrlEmployee}/update`);
    expect(req.request.method).toBe('PUT');
  
    // Simulate a successful response from the backend
    req.flush(employee);
    
    expect(formatNameSpy).toHaveBeenCalledWith('john doe');
  });
```

3. Al agregar y al editar un empleado, controlar que el nombre del empleado no esté repetido.

Nota: en este caso se prueba que Angular reciba y muestre adecuadamente los errores ocurridos en la API.

```typescript
it('should handle API error on updateEmployee', () => {
    const employee = new Employee(1, 'John Doe', '');
  
    // Simulate an error message from the backend
    const errorMessage = 'El empleado ya se encuentra registrado';
    const errorResponse = { status: 400, statusText: 'Bad Request' };
  
    // Call the service method
    service.updateEmployee(employee).subscribe({
      next: () => fail('Expected an error, but got success response'),
      error: (error) => {
        expect(error.status).toBe(400);
        expect(error.statusText).toBe('Bad Request');
      }
    });
  
    // Expect an API call
    const req = httpMock.expectOne(`${service.apiUrlEmployee}/update`);
    expect(req.request.method).toBe('PUT');
  
    // Simulate an API error response (passing the error as a string directly)
    req.flush(errorMessage, errorResponse);
  
    // Verify that the ToastrService error was called
    expect(toastr.error).toHaveBeenCalledWith(errorMessage, 'Error al actualizar empleado');
  });
```

4. Asegurar que cada parte del nombre (separada por espacios) tenga más de un carácter.

```typescript
it('should fail to update employee when a part of name is of length 1', () => {
    const invalidEmployee = new Employee (1, 'J D', '');
    service.updateEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('Los nombres y apellidos del empleado debe tener una longitud mayor a 1 letra.', 'Error de validación');

  });
```

5. La longitud máxima del nombre y apellido del empleado debe ser de 100 caracteres.

```typescript
it('should fail to update employee when name is longer than 100', () => {
    const invalidEmployee = new Employee (1, 'A'.repeat(120), '');
    service.updateEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no puede contener más de 100 letras.', 'Error de validación');

  });
```

6. Evitar que se ingresen caracteres repetidos de forma excesiva.

```typescript
it('should fail to update employee when name contains repeated characters', () => {
    const invalidEmployee = new Employee (1, 'Joooohn', '');
    service.updateEmployee(invalidEmployee).subscribe({
      next: () => fail('Expected validation error'),
      error: (error: Error) => {
        expect(error.message).toContain('Employee name validation failed.');
      }
    });

    expect(toastr.error).toHaveBeenCalledWith('El nombre del empleado no debe contener caracteres repetidos de manera excesiva.', 'Error de validación');

  });
```

### Ejecución de pruebas
Se ejecutan las pruebas escritas (junto con las anteriormente definidas en el proyecto) y se observa que todas ellas finalizan con resultado exitoso.

![Imagen Paso 7i](Paso%207i.jpg)

## Link a proyecto
Se adjunta un enlace al proyecto en GitHub.
[https://github.com/MaximoChattas/Angular_WebAPINetCore8_CRUD_Sample.git](https://github.com/MaximoChattas/Angular_WebAPINetCore8_CRUD_Sample.git)

Nota: se agrega una validación adicional por no poder probar adecuadamente el chequeo de empleados repetidos íntegramente desde el frontend.