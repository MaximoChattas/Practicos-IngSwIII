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
  ConnectedServiceName: 'AzureResourceManager'
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
            buildContext: $(Pipeline.Workspace)/dotnet-drop
            tags: 'latest'

        - task: Docker@2
          displayName: 'Subir Imagen Docker de Back a ACR'
          inputs:
            command: push
            repository: $(acrLoginServer)/$(backImageName)
            tags: 'latest'
```

## Paso 7
Se ejecuta el pipeline con las modificaciones anteriormente descritas, el cual finaliza con éxito.

![Imagen Paso 7a](Paso%207a.jpg)

Se verifica que dentro del recurso ACR anteriormente creado, haya una imagen con el nombre definido dentro de la variable del pipeline.

![Imagen Paso 7b](Paso%207b.jpg)

## Paso 8
A la etapa anteriormente creada (), se le agrega un nuevo job que permite construir y subir la imagen de frontend de Docker al recurso ACR.

```yaml
- job: docker_build_and_push_front
      displayName: 'Construir y Subir Imagen Docker de Front a ACR'
      pool:
        vmImage: 'ubuntu-latest'
        
      steps:
        - checkout: self

        - task: DownloadPipelineArtifact@2
          displayName: 'Descargar Artefactos de Front'
          inputs:
            buildType: 'current'
            artifactName: 'angular-drop'
            targetPath: '$(Pipeline.Workspace)/angular-drop'
        
        - task: DownloadPipelineArtifact@2
          displayName: 'Descargar Dockerfile de Back'
          inputs:
            buildType: 'current'
            artifactName: 'dockerfile-front'
            targetPath: '$(Pipeline.Workspace)/dockerfile-front'

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
            repository: $(acrLoginServer)/$(frontImageName)
            dockerfile: $(Pipeline.Workspace)/dockerfile-front/Dockerfile
            buildContext: $(Pipeline.Workspace)/angular-drop
            tags: 'latest'

        - task: Docker@2
          displayName: 'Subir Imagen Docker de Back a ACR'
          inputs:
            command: push
            repository: $(acrLoginServer)/$(frontImageName)
            tags: 'latest'
```

Además, se agregó una nueva variable donde se define el nombre de la imagen de frontend que será subida al ACR.
```yaml
frontImageName: 'employee-crud-angular'
```

Se ejecuta el pipeline y se observa que el mismo finaliza de manera exitosa.

![Imagen Paso 8a](Paso%208a.jpg)

A su vez, dentro del recurso ACR se pueden ver ambas imágenes correctamente subidas.

![Imagen Paso 8b](Paso%208b.jpg)

## Paso 9
Se agregaron al pipeline las siguientes variables:

```yaml
  acrName: 'mcingsw3uccacr'
  ResourceGroupName: 'IngSw3-UCC'
  backContainerInstanceNameQA: 'chattas-crud-api-qa'
  backImageTag: 'latest' 
  container-cpu-api-qa: 1 #CPUS de nuestro container de QA
  container-memory-api-qa: 1.5 #RAM de nuestro container de QA
```

Desde la interfaz gráfica de Azure DevOps, se agregó una variable secreta "cnn_string_qa" que contiene la cadena de conexión a la base de datos de Azure.
![Imagen Paso 9a](Paso%209.jpg)

Finalmente, se agregó una nueva etapa al pipeline que se encarga de realizar el despliegue de la imagen anteriormente creada en Azure Container Instances, dentro del entorno de QA.

```yaml
- stage: DeployToACIQA
  displayName: 'Desplegar en Azure Container Instances (ACI) QA'
  dependsOn: DockerBuildAndPush
  jobs:
    - job: deploy_to_aci_qa
      displayName: 'Desplegar en Azure Container Instances (ACI) QA'
      pool:
        vmImage: 'ubuntu-latest'
        
      steps:

        - task: AzureCLI@2
          displayName: 'Desplegar Imagen Docker de Back en ACI QA'
          inputs:
            azureSubscription: '$(ConnectedServiceName)'
            scriptType: bash
            scriptLocation: inlineScript
            inlineScript: |
              echo "Resource Group: $(ResourceGroupName)"
              echo "Container Instance Name: $(backContainerInstanceNameQA)"
              echo "ACR Login Server: $(acrLoginServer)"
              echo "Image Name: $(backImageName)"
              echo "Image Tag: $(backImageTag)"
              echo "Connection String: $(cnn_string_qa)"
          
              az container delete --resource-group $(ResourceGroupName) --name $(backContainerInstanceNameQA) --yes

              az container create --resource-group $(ResourceGroupName) \
                --name $(backContainerInstanceNameQA) \
                --image $(acrLoginServer)/$(backImageName):$(backImageTag) \
                --registry-login-server $(acrLoginServer) \
                --registry-username $(acrName) \
                --registry-password $(az acr credential show --name $(acrName) --query "passwords[0].value" -o tsv) \
                --dns-name-label $(backContainerInstanceNameQA) \
                --ports 80 \
                --environment-variables ConnectionStrings__DefaultConnection="$(cnn_string_qa)" \
                --restart-policy Always \
                --cpu $(container-cpu-api-qa) \
                --memory $(container-memory-api-qa)
```

## Paso 10
Se ejecuta el pipeline con las modificaciones anteriormente mencionadas, que finaliza de manera exitosa.

![Imagen Paso 10a](Paso%2010a.jpg)

Dentro de Azure Portal, se observa que hay un nuevo recurso Azure Container Instanes con el nombre definido dentro del pipeline.

![Imagen Paso 10b](Paso%2010b.jpg)

Se navega a la URL provista y se observa que el contenedor se encuentra correctamente conectado a la base de datos.

![Imagen Paso 10c](Paso%2010c.jpg)