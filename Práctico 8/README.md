# Trabajo Práctico 8
## Paso 1
Se crea un directorio "docker" dentro del directorio raíz del proyecto que contiene 2 carpetas (api y front), y dentro de cada una de ellas se crean los correspondientes Dockerfile como se muestra a continuación.

![Imagen Paso 1](Paso%201.jpg)

## Paso 2
Se crea un recurso Azure Container Registry desde el portal de Azure siguiendo el instructivo.

![Imagen Paso 2](Paso%202.jpg)

## Paso 3
Luego de publicar los artefactos de compilación del backend, se agrega el siguiente código dentro del pipeline.

```yaml
- task: PublishPipelineArtifact@1
    displayName: 'Publicar Dockerfile de Back'
    inputs:
    targetPath: '$(Build.SourcesDirectory)/docker/api/dockerfile'
    artifact: 'dockerfile-back'
```

Luego de publicar los artefactos del frontend, se agrega el siguiente código dentro del pipeline.

```yaml
- task: PublishPipelineArtifact@1
    displayName: 'Publicar Dockerfile de Front'
    inputs:
    targetPath: '$(Build.SourcesDirectory)/docker/front/Dockerfile'
    artifact: 'dockerfile-front'
```

Estos publican los Dockerfiles anteriormente creados, lo cual permite que estén disponibles para ser utilizados en etapas posteriores.

## Paso 4
Se agrega una service connection a Azure Portal llamada "AzureResourceManager".

![Imagen Paso 4](Paso%204.jpg)

## Paso 5
Se agregan las siguientes variables dentro del pipeline a las anteriormente creadas.

```yaml
variables:
  ConnectedServiceName: 'Azure Resource Manager'
  acrLoginServer: 'mcingsw3uccacr.azurecr.io'
  backImageName: 'employee-crud-api'
```

## Paso 6
Se agrega una nueva etapa al pipeline en la cual se construye y publica el contenedor backend de Docker en el recurso ACR anteriormente creado.

```yaml
- stage: DockerBuildAndPush
  displayName: 'Construir y Subir Imágenes Docker a ACR'
  dependsOn: BuildAndTest
  jobs:
    - job: docker_build_and_push
      displayName: 'Construir y Subir Imágenes Docker a ACR'
      pool:
        vmImage: 'ubuntu-latest'
        
      steps:
        - checkout: self

        - task: DownloadPipelineArtifact@2
          displayName: 'Descargar Artefactos de Back'
          inputs:
            buildType: 'current'
            artifactName: 'dotnet-drop'
            targetPath: '$(Pipeline.Workspace)/dotnet-drop'
        
        - task: DownloadPipelineArtifact@2
          displayName: 'Descargar Dockerfile de Back'
          inputs:
            buildType: 'current'
            artifactName: 'dockerfile-back'
            targetPath: '$(Pipeline.Workspace)/dockerfile-back'

        - task: AzureCLI@2
          displayName: 'Iniciar Sesión en Azure Container Registry (ACR)'
          inputs:
            azureSubscription: '$(ConnectedServiceName)'
            scriptType: bash
            scriptLocation: inlineScript
            inlineScript: |
              az acr login --name $(acrLoginServer)
    
        - task: Docker@2
          displayName: 'Construir Imagen Docker para Back'
          inputs:
            command: build
            repository: $(acrLoginServer)/$(backImageName)
            dockerfile: $(Pipeline.Workspace)/dockerfile-back/dockerfile
            buildContext: $(Pipeline.Workspace)/drop-back
            tags: 'latest'

        - task: Docker@2
          displayName: 'Subir Imagen Docker de Back a ACR'
          inputs:
            command: push
            repository: $(acrLoginServer)/$(backImageName)
            tags: 'latest'
```