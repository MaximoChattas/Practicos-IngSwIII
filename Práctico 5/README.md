# Trabajo Práctico 5
## Paso 1
Se muestra a continuación que la cuenta de Azure fue creada exitosamente.

![Imagen Paso 1](Paso%201.jpg)

## Paso 2
En Azure, se crea un nuevo recurso "Web App" configurado como se muestra a continuación.

![Imagen Paso 2a](Paso%202a.jpg)

Previo a su creación, se descargó de manera local un template con esta configuración para poder repetir el proceso de forma más sencilla.

Se muestra que el recurso fue desplegado exitosamente.

![Imagen Paso 2b](Paso%202b.jpg)

A continuación se encuentran los detalles del recurso desplegado.

![Imagen Paso 2c](Paso%202c.jpg)

Y se navega hasta la URL provista por el mismo.

![Imagen Paso 2d](Paso%202d.jpg)

## Paso 3
En primer lugar, se deshabilita la opción que prohibe la creación de Classic Release Pipelines.

![Imagen Paso 3a](Paso%203a.jpg)

Luego, se crea un pipeline que permita hacer el deploy del proyecto "Sample02" en el recurso Web App creado anteriormente. Fue configurado CD (continuous deployment) para que el pipeline se ejecute automáticamente cuando haya un nuevo build del proyecto.

![Imagen Paso 3b](Paso%203b.jpg)

Se muestra el resultado con el pipeline renombrado.

![Imagen Paso 3c](Paso%203c.jpg)

## Paso 4
Se actualiza el build pipeline para que utilice tareas de DotNetCoreCLI@2.

![Imagen Paso 4](Paso%204.jpg)

## Paso 5
Para verificar el correcto deploy de la aplicación en el recurso creado anteriormente, en primer lugar se ejecuta el release pipeline, que finaliza con resultado exitoso.

![Imagen Paso 5a](Paso%205a.jpg)

Luego, al navegar a la URL provista [https://chattas-webapp01.azurewebsites.net/weatherforecast](https://chattas-webapp01.azurewebsites.net/weatherforecast) se ve que la aplicación se encuentra corriendo correctamente a partir del último artefacto generado por el build pipeline.

![Imagen Paso 5b](Paso%205b.jpg)

## Paso 6
Para verificar que la secuencia de pipelines se ejecute correctamente, en primer lugar se realizó un cambio en el controller de la aplicación para que muestre 7 pronósticos.

![Imagen Paso 6a](Paso%206a.jpg)

Esto generó que se ejecute el build pipeline que creó el artefacto nuevo con las modificaciones realizadas (CI).

![Imagen Paso 6b](Paso%206b.jpg)

Una vez finalizado de manera exitosa, comienza automáticamente la ejecución del release pipeline que realiza un deploy de la aplicación con la actualización (CD).

![Imagen Paso 6c](Paso%206c.jpg)

Al navegar a la correspondiente URL, se observa la nueva versión de la aplicación, con los 7 pronósticos.

![Imagen Paso 6d](Paso%206d.jpg)

## Paso 7
A partir del template descargado durante la creación de la Web App en el [Paso 2](#paso-2), utilizando el recurso de Azure "Custom Deployment", se crea una nueva Web App. De este modo, se utiliza la misma configuración que en la anterior, sin necesidad de especificar todo nuevamente. Lo único que fue modificado, es su nombre.

![Imagen Paso 7a](Paso%207a.jpg)

Como se muestra a continuación, el despliegue fue exitoso.

![Imagen Paso 7b](Paso%207b.jpg)

Se observa que ambas Web App se encuentran dentro del Resource Group creado.

![Imagen Paso 7c](Paso%207c.jpg)

Finalmente, navegamos a la URL provista por la segunda Web App [(https://chattas-webapp01-prod.azurewebsites.net)](https://chattas-webapp01-prod.azurewebsites.net), donde se encuentra la página por defecto de Azure.

![Imagen Paso 7d](Paso%207d.jpg)

## Paso 8
Para hacer uso de la segunda Web App desplegada anteriormente, se agrega una nueva etapa al release pipeline previamente creado, que permite realizar un deploy en la Web App de producción (Prod).

![Imagen Paso 8a](Paso%208a.jpg)

Las configuraciones que se utilizaron en esta etapa se encuentran detalladas a continuación.

![Imagen Paso 8b](Paso%208b.jpg)

## Paso 9
A continuación, se realiza un cambio en el controlador de la aplicación para que la misma devuelva 10 pronósticos.

![Imagen Paso 9a](Paso%209a.jpg)

Este commit realizado sobre la main branch, disparó automáticamente la ejecución del build y posteriormente, release pipelines.

![Imagen Paso 9b](Paso%209b.jpg)
Nota: en la imagen se observa un tercer pipeline, este es un build pipeline creado para prácticos anteriores utilizando el classic editor con CI configurado, pero no es relevante para este proceso.

Se observa a continuación que los cambios fueron desplegados en ambos servidores.

### QA
![Imagen Paso 9c](Paso%209c.jpg)

### Prod
![Imagen Paso 9d](Paso%209d.jpg)

## Paso 10
Se modifica el release pipeline de modo que se requiera una aprobación manual para desplegar la aplicación en el servidor de prod.

![Imagen Paso 10](Paso%2010.jpg)
La propiedad "Timeout" hace referencia a la cantidad de tiempo que la aprobación puede permanecer pendiente antes de ser rechazada automáticamente. En este caso, se mantuvo su valor por defecto de 30 días.

## Paso 11
Para verificar que la aprobación manual se haya configurado correctamente, se realizó un nuevo cambio en el código, definiendo que el controlador devuelva 5 días de pronóstico.

![Imagen Paso 11a](Paso%2011a.jpg)

Este commit disparó la ejecución del build pipeline, que finalizó con éxito.

![Imagen Paso 11b](Paso%2011b.jpg)

Al finalizar exitosamente la ejecución del build pipeline, comienza la ejecución del release pipeline que también finalizó de manera exitosa.

![Imagen Paso 11c](Paso%2011c.jpg)
Como se puede observar, en el release pipeline hay una aprovación pendiente.

En las URL provistas, se observa que en el ambiente de QA el cambio se encuentra implementado, habiendo sólo 5 pronósticos.

![Imagen Paso 11d](Paso%2011d.jpg)

Por otro lado, debido a que no fue aprobado manualmente el cambio, en el ambiente de producción continúa estando la versión anterior.

![Imagren Paso 11e](Paso%2011e.jpg)

## Paso 12
Se muestra desde el release pipeline que hay una aprobación manual pendiente en la etapa "Deploy to Prod".

![Imagen Paso 12a](Paso%2012a.jpg)

A continuación se aprueba el cambio desde el portal de Azure DevOps.

![Imagen Paso 12b](Paso%2012b.jpg)

De este modo, el deploy a prod se completa con éxito.

![Imagen Paso 12c](Paso%2012c.jpg)

## Paso 13
Se muestra que en el ambiente de producción, el sitio fue actualizado a la última versión con 5 pronósticos.

![Imagen Paso 13](Paso%2013.jpg)

## Paso 14
Para crear un release pipeline utilizando un archivo YAML que permita hacer los deploy en QA y PROD, se utilizaron múltiples fases o "stages". Este pipeline descarga el artifact generado por el build pipeline "Sample02" creado en prácticos anteriores, y se ejecuta en 3 etapas separadas:
1. Deploy a QA.
2. Aprobación manual para PROD.
3. Deploy a Prod.

Cada etapa es dependiente de la anterior, por lo que en caso de fallo, el pipeline no continúa ejecutándose. A su vez, mediante el uso de triggers, el pipeline se ejecuta automáticamente después de que finalize exitósamente el build pipeline asociado (también construido en YAML).

A continuación, se presenta el código creado para este paso.

```yaml
trigger:
- none

# Sets the build pipeline from which it gets the artifact to deploy
resources:
  pipelines:
    - pipeline: 'BuildPipeline'
      project: 'Sample02'
      source: 'Sample02'
      trigger:
        branches:
        - main

stages:
  - stage: DeployToQA
    displayName: 'Deploy site to QA'
    pool:
      vmImage: 'windows-latest'
      
    jobs:
    - job: DeployQA
      displayName: 'Deploy to QA'
      steps:
      - download: 'BuildPipeline'
        displayName: 'Download Build Artifacts'
        artifact: 'drop'

      - task: AzureWebApp@1
        inputs:
          azureSubscription: 'Azure subscription 1 (a66d8dc3-948a-4b74-94d7-4e0e2aef3a70)'
          appType: 'webApp'
          appName: 'Chattas-WebApp01'
          package: '$(Pipeline.Workspace)/BuildPipeline/**/*.zip'
  
  - stage: ManualApproval
    displayName: 'Wait for Manual Approval'
    dependsOn: DeployToQA
    condition: succeeded()
    
    jobs:
    - job: waitForValidation
      displayName: Wait for external validation
      pool: server
      timeoutInMinutes: 10080 # 7 days
      steps:
      - task: ManualValidation@0
        timeoutInMinutes: 10080 # 7 days
        inputs:
          notifyUsers: |
            2112737@ucc.edu.ar
          instructions: 'Validate build and approve release to PROD'
          onTimeout: 'reject'

  - stage: DeployToProd
    displayName: 'Deploy site to PROD'
    dependsOn: ManualApproval
    condition: succeeded()
    pool:
      vmImage: 'windows-latest'

    jobs:
      - job: DeployPROD
        displayName: 'Deploy to PROD'
        steps:
          - download: 'BuildPipeline'
            displayName: 'Download Build Artifacts'
            artifact: 'drop'
          - task: AzureWebApp@1
            inputs:
              azureSubscription: 'Azure subscription 1 (a66d8dc3-948a-4b74-94d7-4e0e2aef3a70)'
              appType: 'webApp'
              appName: 'Chattas-WebApp01-PROD'
              package: '$(Pipeline.Workspace)/BuildPipeline/**/*.zip'
```

A continuación, se muestra el paso a paso de CI y CD utilizando estos pipelines.

### Cambio en el Código
Se introduce un cambio en el código que modifica la cantidad de pronósticos que envía la API a 2.

![Imagen Paso 14a](Paso%2014a.jpg)

### Continuous Integration
El build pipeline "Sample 02" se ejecuta automáticamente luego del commit realizado y crea los artifacts con la nueva versión de la aplicación.

![Imagen Paso 14b](Paso%2014b.jpg)

### Continuous Deployment
Al finalizar exitósamente la ejecución del build pipeline, comienza la ejecución del release pipeline mostrado anteriormente. Este se detiene en la segunda etapa, aguardando la validación manual antes de hacer el deploy al ambiente de producción.

![Imagen Paso 14c](Paso%2014c.jpg)

Como se muestra a continuación, el ambiente de QA ya dispone de la nueva versión, con sólo 2 pronósticos.

![Imagen Paso 14d](Paso%2014d.jpg)

Mientras que producción continúa con la versión anterior (con 15 pronósticos).

![Imagen Paso 14e](Paso%2014e.jpg)

#### Aprobación Manual

Se aprueba manualmente el deploy.

![Imagen Paso 14f](Paso%2014f.jpg)

El release pipeline finaliza su ejecución con éxito.

![Imagen Paso 14g](Paso%2014g.jpg)

Y en el ambiente de producción ya se dispone de la nueva versión del producto.

![Imagen Paso 14h](Paso%2014h.jpg)

#### Rechazo Manual
En caso de rechazar el deploy a producción, el pipeline finaliza de manera errónea, habiendo realizado el deploy en el ambiente de QA, pero no así en PROD.

![Imagen Paso 14i](Paso%2014i.jpg)