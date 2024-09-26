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

Dentro de este proyecto, se creó un build pipeline para la API de .NET y la aplicación de Angular que incluye la ejecución y publicación de los resultados de los casos de prueba. El pipeline cuenta con 2 etapas, una para cada parte del proyecto. Se muestra a continuación el código creado.

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
# Stage 1: DOTNET API
- stage: DOTNET_API
  displayName: "DOTNET API"
  jobs:
  - job: BuildAndTest
    displayName: "Build and Test API"
    pool:
      vmImage: 'windows-latest'
    steps:
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

# Stage 2: Angular App
- stage: Angular_App
  displayName: "Angular App"
  jobs:
  - job: BuildAngular
    displayName: "Build Angular Project"
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