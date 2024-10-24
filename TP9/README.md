## Trabajo Práctico 9 - Implementación de Contenedores en Azure Parte 2

### 4- Desarrollo:

#### 4.1 Modificar nuestro pipeline para incluir el deploy en QA y PROD de Imagenes Docker en Servicio Azure App Services con Soporte para Contenedores
- Desarrollo del punto 4.1: 
	
  	- ##### 4.1.1 - Agregar a nuestro pipeline una nueva etapa que dependa de nuestra etapa de Construcción y Pruebas y de la etapa de Construcción de Imagenes Docker y subida a ACR realizada en el TP08
  	    
  	  - Agregar tareas para crear un recurso Azure Container Instances que levante un contenedor con nuestra imagen de back utilizando un AppServicePlan en Linux
  	  ```yaml
		#---------------------------------------
		### STAGE DEPLOY TO AZURE APP SERVICE QA
		#---------------------------------------
		- stage: DeployImagesToAppServiceQA
		  displayName: 'Desplegar Imagenes en Azure App Service (QA)'
		  dependsOn: 
		  - BuildAndTestBackAndFront
		  - DockerBuildAndPush
		  condition: succeeded()
		  jobs:
		    - job: DeployImagesToAppServiceQA
		      displayName: 'Desplegar Imagenes de API y Front en Azure App Service (QA)'
		      pool:
		        vmImage: 'ubuntu-latest'
		      steps:
		        #------------------------------------------------------
		        # DEPLOY DOCKER API IMAGE TO AZURE APP SERVICE (QA)
		        #------------------------------------------------------
		        - task: AzureCLI@2
		          displayName: 'Verificar y crear el recurso Azure App Service para API (QA) si no existe'
		          inputs:
		            azureSubscription: '$(ConnectedServiceName)'
		            scriptType: 'bash'
		            scriptLocation: 'inlineScript'
		            inlineScript: |
		              # Verificar si el App Service para la API ya existe
		              if ! az webapp list --query "[?name=='$(WebAppApiNameContainersQA)' && resourceGroup=='$(ResourceGroupName)'] | length(@)" -o tsv | grep -q '^1$'; then
		                echo "El App Service para API QA no existe. Creando..."
		                # Crear el App Service sin especificar la imagen del contenedor
		                az webapp create --resource-group $(ResourceGroupName) --plan $(AppServicePlanLinux) --name $(WebAppApiNameContainersQA) --deployment-container-image-name "nginx"  # Especifica una imagen temporal para permitir la creación
		              else
		                echo "El App Service para API QA ya existe. Actualizando la imagen..."
		              fi
		
		              # Configurar el App Service para usar Azure Container Registry (ACR)
		              az webapp config container set --name $(WebAppApiNameContainersQA) --resource-group $(ResourceGroupName) \
		                --container-image-name $(acrLoginServer)/$(backImageName):$(backImageTag) \
		                --container-registry-url https://$(acrLoginServer) \
		                --container-registry-user $(acrName) \
		                --container-registry-password $(az acr credential show --name $(acrName) --query "passwords[0].value" -o tsv)
		              # Establecer variables de entorno
		              az webapp config appsettings set --name $(WebAppApiNameContainersQA) --resource-group $(ResourceGroupName) \
		                --settings ConnectionStrings__DefaultConnection="$(cnn-string-qa)" \
	
  	  ```

1. Creamos el AppServicePlanLinux en Azure Portal  
- ![alt text](imagenes/0.png)	  
- ![alt text](imagenes/1.png)

2. Creamos en el pipeline las siguientes variables:
- ![alt text](imagenes/2.png)
- ![alt text](imagenes/3.png)

3. Corremos el pipeline para crear el App Service:
- ![alt text](imagenes/4.png)
- ![alt text](imagenes/5.png)
- ![alt text](imagenes/6.png)

#### 4.2 Desafíos:
- 4.2.1 Agregar tareas para generar Front en Azure App Service con Soporte para Contenedores
	- ![alt text](imagenes/7.png)
	- Creamos la siguiente variable en el Pipeline:
		- ![alt text](imagenes/8.png)
	
- ![alt text](imagenes/10.png)
- ![alt text](imagenes/9.png)
- ![alt text](imagenes/13.png)


- 4.2.2 Agregar variables necesarias para el funcionamiento de la nueva etapa considerando que debe haber 2 entornos QA y PROD para Back y Front.
- ![alt text](imagenes/11.png)
- ![alt text](imagenes/12.png)

- 4.2.3 Agregar tareas para correr pruebas de integración en el entorno de QA de Back y Front creado en Azure App Services con Soporte para Contenedores. 
- ![alt text](imagenes/14.png)
- ![alt text](imagenes/15.png)

- 4.2.4 Agregar etapa que dependa de la etapa de Deploy en QA que genere un entorno de PROD.
- ![alt text](imagenes/16.png)
- ![alt text](imagenes/17.png)
- ![alt text](imagenes/18.png)
- ![alt text](imagenes/19.png)
- ![alt text](imagenes/20.png)
- ![alt text](imagenes/21.png)
- ![alt text](imagenes/22.png)

- 4.2.5 Entregar un pipeline que incluya:
  - A) Etapa Construcción y Pruebas Unitarias y Code Coverage Back y Front
  - ![alt text](imagenes/23.png)

  - B) Construcción de Imágenes Docker y subida a ACR
  - ![alt text](imagenes/24.png)

  - C) Deploy Back y Front en QA con pruebas de integración para Azure Web Apps
  - ![alt text](imagenes/25.png)
  - ![alt text](imagenes/26.png)
  - ![alt text](imagenes/27.png)

  - D) Deploy Back y Front en QA con pruebas de integración para ACI
  - ![alt text](imagenes/28.png)
  - ![alt text](imagenes/29.png)
  - ![alt text](imagenes/30.png)
  - ![alt text](imagenes/31.png)
  - ![alt text](imagenes/32.png)

  - E) Deploy Back y Front en QA con pruebas de integración para Azure Web Apps con Soporte para contenedores
  - ![alt text](imagenes/33.png)

  - F) Aprobación manual de QA para los puntos C,D,E
  - G) Deploy Back y Front en PROD para Azure Web Apps
  - ![alt text](imagenes/34.png)

  - H) Deploy Back y Front en PROD para ACI
  - ![alt text](imagenes/35.png)

  - I) Deploy Back y Front en PROD para Azure Web Apps con Soporte para contenedores
  - ![alt text](imagenes/36.png)

- Pipeline Completo: (Link: https://dev.azure.com/fgazzera/Sample02/_build/results?buildId=199&view=results)
- ![alt text](imagenes/00.png)

### 6-  Presentación del trabajo práctico.
- Subir un doc al repo de GitHub con las capturas de pantalla de los pasos realizados. Debe ser un documento (md, word, o pdf), no videos. Y el documento debe seguir los pasos indicados en el Desarrollo del TP.
- Acceso al repo de Azure Devops para revisar el trabajo realizado.

### 7-  Criterio de Calificación
El paso 4.1 representa un 20% de la nota total, el paso 4.2 representa el 80% restante.

