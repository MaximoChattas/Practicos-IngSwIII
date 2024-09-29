# Trabajo Práctico 7

## Paso 1
Se instala la dependencia de Karma-Coverage en Angular mediante el comando:
```bash
npm install karma-coverage --save-dev
```

![Imagen Paso 1a](Paso%201a.jpg)

Se edita el archivo karma.conf.js con el código provisto.

![Imagen Paso 1b](Paso%201b.jpg)

Se agrega el paquete coverlet.collector en el proyecto de Dotnet mediante el comando:
```bash
dotnet add package coverlet.collector
```

![Imagen Paso 1c](Paso%201c.jpg)

Se crea un nuevo proyecto en Azure DevOps (Angular-Dotnet Project), en el cual fue importado el repositorio del trabajo práctico 6 con los cambios anteriormente realizados.

![Imagen Paso 1d](Paso%201d.jpg)

Dentro de este proyecto, se creó un build pipeline para la API de .NET y la aplicación de Angular que incluye la ejecución y publicación de los resultados de los casos de prueba. El pipeline cuenta con 1 etapa, con 2 jobs. Se muestra a continuación el código creado.

```yaml
trigger:
- main

pool:
  vmImage: 'windows-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'
  frontPath: './EmployeeCrudAngular'

stages:
- stage: BuildAndTest
  displayName: "Build and Test API and Front"
  jobs:
  - job: BuildDotnet
    displayName: "Build and Test API"
    pool:
      vmImage: 'windows-latest'
    steps:
    - checkout: self
      fetchDepth: 0

    - task: DotNetCoreCLI@2
      displayName: 'Restaurar paquetes NuGet'
      inputs:
        command: restore
        projects: '$(solution)'

    - task: DotNetCoreCLI@2
      displayName: 'Ejecutar pruebas de la API'
      inputs:
        command: 'test'
        projects: '**/*.Tests.csproj'
        arguments: '--collect:"XPlat Code Coverage"'

    - task: PublishCodeCoverageResults@2
      displayName: 'Publicar resultados de code coverage del back-end'
      inputs:
        summaryFileLocation: '$(Agent.TempDirectory)/**/*.cobertura.xml'
        failIfCoverageEmpty: false
      
    - task: DotNetCoreCLI@2
      displayName: 'Compilar la API'
      inputs:
        command: build
        projects: '$(solution)'
        arguments: '--configuration $(buildConfiguration)'

    - task: DotNetCoreCLI@2
      displayName: 'Publicar aplicación'
      inputs:
        command: publish
        publishWebProjects: True
        arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
        zipAfterPublish: true

    - task: PublishBuildArtifacts@1
      displayName: 'Publicar artefactos de compilación'
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: 'api-drop'
        publishLocation: 'Container'

  - job: BuildAngular
    displayName: "Build and Test Angular"
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: NodeTool@0
      displayName: 'Instalar Node.js'
      inputs:
        versionSpec: '22.x'
    
    - script: npm install
      displayName: 'Instalar dependencias'
      workingDirectory: $(frontPath)

    - script: npx ng test --karma-config=karma.conf.js --watch=false --browsers ChromeHeadless --code-coverage
      displayName: 'Ejecutar pruebas del front'
      workingDirectory: $(frontPath)
      continueOnError: true  # Continue on test failure

    - task: PublishCodeCoverageResults@2
      displayName: 'Publicar resultados de code coverage del front'
      inputs:
        summaryFileLocation: '$(frontPath)/coverage/lcov.info'
        failIfCoverageEmpty: false
      condition: always()
    - task: PublishTestResults@2
      displayName: 'Publicar resultados de pruebas unitarias del front'
      inputs:
        testResultsFormat: 'JUnit'
        testResultsFiles: '$(frontPath)/test-results/test-results.xml'
        failTaskOnFailedTests: true
      condition: always()

    - script: npm run build
      displayName: 'Compilar el proyecto Angular'
      workingDirectory: $(frontPath)

    - task: PublishBuildArtifacts@1
      displayName: 'Publicar artefactos Angular'
      inputs:
        PathtoPublish: '$(frontPath)/dist'
        ArtifactName: 'front-drop'
```

Se ejecuta el pipeline que finaliza de manera exitosa.

![Imagen Paso 1e](Paso%201e.jpg)

A continuación se muestran los resultados de los casos de prueba publicados.

![Imagen Paso 1f](Paso%201f.jpg)

Los 31 casos de prueba resultaron exitosos (15 de .NET y 16 de Angular).

Además, se muestra el reporte de cobertura de código.

![Imagen Paso 1g](Paso%201g.jpg)

En los resultados, se obtuvo que las pruebas cubren un 72,61% del total de las líneas de código del proyecto. Esto incluye la API de .NET y la aplicación de Angular.

## Paso 2
Siguiendo el instructivo, se integra SonarCloud a la organización de Azure DevOps, instalando y configurando la extensión correspondiente.

![Imagen Paso 2a](Paso%202a.jpg)

Luego, se agrega dentro del Pipeline del paso anterior las siguientes tareas.

Antes del build de back:
```yaml
- task: SonarCloudPrepare@2
      inputs:
        SonarCloud: 'SonarCloud'
        organization: 'maxichattas'
        scannerMode: 'MSBuild'
        projectKey: 'maxichattas_Angular-Dotnet-Project'
        projectName: 'Angular-Dotnet Project'
```

Después del build de back:
```yaml
    - task: SonarCloudAnalyze@2
      inputs:
        jdkversion: 'JAVA_HOME_17_X64'
    
    - task: SonarCloudPublish@2
      inputs:
        pollingTimeoutSec: '300'
```

Una vez ejecutado el pipeline con las nuevas tareas, se tiene en el resultado un link al análisis realizado por SonarCloud.

![Imagen Paso 2b](Paso%202b.jpg)

Ingresando al link, podemos acceder al detalle del análisis.

![Imagen Paso 2c](Paso%202c.jpg)

Uno de los apartados más importantes del análisis de SonarCloud se encuentra en el apartado de "Security Hotspots", donde se pueden ver las vulnerabilidades de seguridad encontradas en el código. En este caso, se encontraron 2 de ellas, siendo que la más importante se refiere a la cadena de conexión con la base de datos, que tiene la contraseña escrita en duro (hard-code). La segunda vulnerabilidad encontrada se refiere a la política de CORS (Cross Origin Resource Sharing), que está configurada en su modo más permisivo (Allow Any).

![Imagen Paso 2d](Paso%202d.jpg)

Dentro del apartado "Measures" se tienen algunas métricas del código, como su complejidad ciclomática, cobertura de los casos de prueba, confiabilidad, mantenibilidad y demás cuestiones referidas a la calidad del mismo. A su vez, en "Issues", la aplicación ofrece algunas recomendaciones respecto a buenas prácticas para mejorar la calidad del código.

## Paso 3
En primer lugar, se instala la dependencia de Cypress dentro del directorio raíz del proyecto de Angular mediante el comando:
```bash
npm install cypress --save-dev
```

![Imagen Paso 3a](Paso%203a.jpg)

Luego, se abre Cypress mediante el comando:
```bash
npx cypress open
```

![Imagen Paso 3b](Paso%203b.jpg)

Esto inicia Cypress, en donde se configuran los tests de tipo E2E (end to end), como se muestra a continuación.

![Imagen Paso 3c](Paso%203c.jpg)

Se ejecuta una de las pruebas genéricas generadas por Cypress, para verificar su correcto funcionamiento.

![Imagen Paso 3d](Paso%203d.jpg)

Dentro de la carpeta cypress/e2e creamos el primer caso de prueba que verifica que el título del sitio sea "EmployeeCrudAngular". La URL que debe visitar es "http://localhost:4200" debido a que el proyecto corre de forma local en mi dispositivo.

![Imagen Paso 3e](Paso%203e.jpg)

Se observa que la prueba finaliza con resultado exitoso.

![Imagen Paso 3f](Paso%203f.jpg)

A su vez, es posible ejecutar las pruebas sin la interfaz gráfica, es decir, en modo "headless". Esto se hace mediante el comando:
```bash
npx cypress run
```

Se observa que la prueba escrita anteriormente se ejecuta con éxito (junto con las demás pruebas genéricas escritas por Cypress).

![Imagen Paso 3g](Paso%203g.jpg)

Para simular un fallo, se modifica el título esperado dentro de la prueba escrita anteriormente. Además, se quitaron las pruebas genéricas para agilizar el proceso y que no interfieran con los resultados.

![Imagen Paso 3h](Paso%203h.jpg)

Esta prueba fallida, creó un screenshot dentro de la carpeta "cypress/screenshots" mostrando la razón por la cual falló.

![Imagen Paso 3i](Paso%203i.jpg)

Luego, se edita el archivo "cypress.config.ts" definiendo las configuraciones del reporte que debe generar, y habilitando experimentalStudio. Esto permitirá generar nuevas pruebas mediante la interacción con la página.

![Imagen Paso 3j](Paso%203j.jpg)

Utilizando esta herramienta, se prueba la creación de un nuevo empleado con mi nombre completo.

![Imagen Paso 3k](Paso%203k.jpg)

El test inicialmente falla debido a la validación de empleado duplicado definida en el práctico anterior (al intentar crear más de una vez un empleado con el mismo nombre). Por este motivo, se agrega dentro del mismo la eliminación del empleado, luego de validar que el mismo se crea correctamente. Luego de realizar algunos ajustes dentro del código generado, se obtiene la siguiente prueba.
```javascript
/* ==== Generated with Cypress Studio ==== */
      cy.get('.btn').click();
      cy.get('.form-control').clear('N');
      cy.get('.form-control').type('New employee');
      cy.get('.btn').click();
      cy.get('tr:last-child > :nth-child(2)').should('have.text', ' New EMPLOYEE ');
      cy.get('tr:last-child > :nth-child(5) > a > .fa').click();
      /* ==== End Cypress Studio ==== */
```

Se crea un nuevo archivo de prueba llamado "editEmployee_test.cy.js" en el que se prueba la edición de un empleado.
```javascript
describe('editEmployeeTest', () => {
    it('Carga correctamente la página de ejemplo', () => {
      cy.visit('http://localhost:4200') // Colocar la url local o de Azure de nuestro front
      /* ==== Generated with Cypress Studio ==== */
      cy.get(':nth-child(2) > :nth-child(4) > a > .fa').click();

    
      cy.get('.form-control')
      .should('be.visible')
      .should('not.be.disabled')

      cy.wait(500);

      cy.get('.form-control').type('{selectall}{backspace}');

      cy.wait(500);

      cy.get('.form-control').should('have.value', '');

      cy.get('.form-control').type('updated new employee');

      cy.get('.btn').click();

      cy.get('.table > :nth-child(2) > :nth-child(2)').should('have.text', ' Updated New EMPLOYEE ');
      
      cy.get(':nth-child(2) > :nth-child(5) > a > .fa').click();
      /* ==== End Cypress Studio ==== */
    })
  })
```

Se verifica que todas las pruebas escritas anteriormente finalicen con éxito. Para esto, se corre el comando "npx cypress run" en la terminal.

![Imagen Paso 3l](Paso%203l.jpg)

## Paso 4

### Integración de SonarCloud en Angular
Para integrar la revisión estática de código de SonarCloud para el código del proyecto de Angular, que se encuentra dentro del mismo repositorio que el código de la API de .NET, fue necesario crear un proyecto monorepo dentro de la herramienta de análisis. De este modo, se pueden obtener reportes separados por cada una de las partes del proyecto.

Para esto, se creó un nuevo proyecto dentro del cual será realizado el análisis del código de Angular, y siguiendo el instructivo provisto por SonarCloud, se agregó el siguiente código dentro del build pipeline.

Antes del build de Angular:
```yaml
- task: SonarCloudPrepare@2
      inputs:
        SonarCloud: 'SonarCloudFront'
        organization: 'maxichattas'
        scannerMode: 'CLI'
        configMode: 'manual'
        cliProjectKey: 'maxichattas-Angular_Project'
        cliProjectName: 'maxichattas-Angular_Project'
        cliSources: '.'
```

Luego del build de Angular:
```yaml
- task: SonarCloudAnalyze@2
      inputs:
        jdkversion: 'JAVA_HOME_17_X64'
    
- task: SonarCloudPublish@2
  inputs:
    pollingTimeoutSec: '300'
```

A su vez, se creó el archivo "sonar-project.properties" dentro del directorio raíz del proyecto de Angular con el siguiente contenido:
```properties
sonar.projectKey=maxichattas-Angular_Project
sonar.organization=maxichattas

# This is the name and version displayed in the SonarCloud UI.
#sonar.projectName=maxichattas-Angular_Project
#sonar.projectVersion=1.0


# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
#sonar.sources=.

# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8
```

Una vez implementadas estas modificaciones, se ejecuta el pipeline nuevamente. Dentro de sus resutlados, además de los casos de prueba y cobertura de código, se tiene en el apartado de "Extensions" los reportes generados por SonarCloud para Angular y Dotnet.

![Imagen Paso 4a](Paso%204a.jpg)

Ingresando a los links provistos, se puede ver en detalle los análisis realizados.

#### Proyecto de Angular

![Imagen Paso 4b](Paso%204b.jpg)

Dentro del análisis del código de frontend, no se encontró ninguna vulnerabilidad en términos de seguridad, pero sí algunos inconvenientes (issues). En total fueron 36, y se clasificaron de la siguiente manera:
- Importancia:
  - Alta: 0
  - Media: 22
  - Baja: 14
  
- Atributo de calidad que afecta:
  - Seguridad: 0
  - Confiabilidad: 6
  - Mantenibilidad: 30

Dentro de los issues detallados en el análisis, se encuentran varias recomendaciones, como agregar atributos "alt" a las imágenes, importaciones no utilizadas (como "Params", "withInterceptors" y "LOCALE_ID"), variables que nunca se reasignan por lo que deberían ser marcadas como "readOnly". A su vez, detectó algunas dependencias deprecadas que no deberían ser usadas y otras dependencias que fueron importadas múltiples veces dentro del proyecto. Si bien ninguno de los errores detectados resultan críticos, resolver este tipo de cuestiones permite mejorar la calidad del código aumentando su consistencia y facilitando su posterior mantenimiento.

#### Proyecto de Dotnet

![Imagen Paso 4c](Paso%204c.jpg)

Dentro del análisis del código de backend, se encontraron un total de 16 problemas (issues) que se clasifican de la siguiente manera:
- Importancia:
  - Alta: 2
  - Media: 8
  - Baja: 6

- Atributo de calidad que afecta:
  - Seguridad: 0
  - Confiabilidad: 3
  - Mantenibilidad: 13

A su vez, se encontraron 2 vulnerabilidades de seguridad. La más importante se refiere a la cadena de conexión con la base de datos, que tiene la contraseña escrita en duro (hard-code). La segunda vulnerabilidad encontrada se refiere a la política de CORS (Cross Origin Resource Sharing), que está configurada en su modo más permisivo (Allow Any).

En este caso, SonarCloud está señalando algunas cuestiones fundamentales que afectan directamente la seguridad y la integridad de la API y los datos que esta gestiona. Además, sugiere algunas modificaciones dentro del código que aumentarían su calidad, como métodos que deberían ser marcados como estáticos (static) o funciones que deberían ser asíncronas (async).