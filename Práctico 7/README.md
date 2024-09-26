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

Se ejecuta el pipeline y ambas etapas finalizan de manera exitosa.

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