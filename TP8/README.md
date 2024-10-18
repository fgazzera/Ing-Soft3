## Trabajo Práctico 8 - Implementación de Contenedores en Azure y Automatización con Azure CLI


### 4- Desarrollo:
#### Prerequisitos:
 - Azure CLI instalado 
![alt text](imagenes/0.png)


#### 4.1 Modificar nuestro pipeline para construir imágenes Docker de back y front y subirlas a ACR
- Desarrollo del punto 4.1: 
	- ##### 4.1.1 Crear archivos DockerFile para nuestros proyectos de Back y Front
   	  - En la raiz de nuestro repo crear una carpeta docker con dos subcarpetas api y front, dentro de cada una de ellas colocar los dockerfiles correspondientes para la creación de imágenes docker en función de la salida de nuestra etapa de Build y Test
  
	  - ![alt text](imagenes/1.png)


   	- ##### 4.1.2 Crear un recurso ACR en Azure Portal siguiendo el instructivo 5.1
		- ![alt text](imagenes/2.png)
		- ![alt text](imagenes/3.png)
		- ![alt text](imagenes/4.png)

  	- ##### 4.1.3 Modificar nuestro pipeline en la etapa de Build y Test
   	  - Luego de la tarea de publicación de los artefactos de Back agregar la tarea de publicación de nuestro dockerfile de back para que esté disponible en etapas posteriores:
   			- ![alt text](imagenes/5.png)
   	  - Luego de la tarea de publicación de los artefactos de Front agregar la tarea de publicación de nuestro dockerfile de front para que esté disponible en etapas posteriores:
   	    	- ![alt text](imagenes/6.png)

  	- ##### 4.1.4 En caso de no contar en nuestro proyecto con una ServiceConnection a Azure Portal para el manejo de recursos, agregar una service connection a Azure Resource Manager como se indica en instructivo 5.2 
		- ![alt text](imagenes/7.png)

  	- ##### 4.1.5 Agregar a nuestro pipeline variables 
		```yaml
		trigger:
		- main
		
		pool:
		  vmImage: 'windows-latest'
	
		# AZURE VARIABLES
		variables: 
		  ConnectedServiceName: 'NOMBRE_SERVICE_CONNECTION_AZURE_RESOURCE_MANAGER' #Por ejemplo 'ServiceConnectionARM'
		  acrLoginServer: 'URL_DE_RECURSO_ACR' #Por ejemplo 'ascontainerregistry.azurecr.io'
		  backImageName: 'NOMBRE_IMAGEN_QA' #Por ejemplo 'employee-crud-api'
	
		```
  	- ##### 4.1.6 Agregar a nuestro pipeline una nueva etapa que dependa de nuestra etapa de Build y Test
  	  - Agregar tareas para generar imagen Docker de Back
   	  
   	     ```yaml
		# #----------------------------------------------------------
		# ### STAGE BUILD DOCKER IMAGES Y PUSH A AZURE CONTAINER REGISTRY
		# #----------------------------------------------------------
		
		- stage: DockerBuildAndPush
		  displayName: 'Construir y Subir Imágenes Docker a ACR'
		  dependsOn: BuildAndTestBackAndFront #NOMBRE DE NUESTRA ETAPA DE BUILD Y TEST
		  jobs:
		    - job: docker_build_and_push
		      displayName: 'Construir y Subir Imágenes Docker a ACR'
		      pool:
		        vmImage: 'ubuntu-latest'
		
		      steps:
		        - checkout: self
		
		        #----------------------------------------------------------
		        # BUILD DOCKER BACK IMAGE Y PUSH A AZURE CONTAINER REGISTRY
		        #----------------------------------------------------------
		
		        - task: DownloadPipelineArtifact@2
		          displayName: 'Descargar Artefactos de Back'
		          inputs:
		            buildType: 'current'
		            artifactName: 'drop-back'
		            targetPath: '$(Pipeline.Workspace)/drop-back'
		        
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
	  ```yaml
  	
  	- ##### 4.1.7 - Ejecutar el pipeline y en Azure Portal acceder a la opción Repositorios de nuestro recurso Azure Container Registry. Verificar que exista una imagen con el nombre especificado en la variable backImageName asignada en nuestro pipeline
- ![alt text](imagenes/8.png)
                             
	- ##### 4.1.8 - Agregar tareas para generar imagen Docker de Front (DESAFIO)
  	  - A la etapa creada en 4.1.6 Agregar tareas para generar imagen Docker de Front
	  
	    ```yaml
		    #----------------------------------------------------------
			# BUILD DOCKER FRONT IMAGE Y PUSH A AZURE CONTAINER REGISTRY
			#----------------------------------------------------------

				- task: DownloadPipelineArtifact@2
					displayName: 'Descargar Artefactos de Front'
					inputs:
					buildType: 'current'
					artifactName: 'Front'
					targetPath: '$(Pipeline.Workspace)/Front'

				- task: DownloadPipelineArtifact@2
					displayName: 'Descargar Dockerfile de Front'
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
					displayName: 'Construir Imagen Docker para Front'
					inputs:
					command: build
					repository: $(acrLoginServer)/$(frontImageName)
					dockerfile: $(Pipeline.Workspace)/dockerfile-front/dockerfile
					buildContext: $(Pipeline.Workspace)/Front
					tags: 'latest'
					
				- task: Docker@2
					displayName: 'Subir Imagen Docker de Front a ACR'
					inputs:
					command: push
					repository: $(acrLoginServer)/$(frontImageName)
					tags: 'latest'

	  ```yaml

- ![alt text](imagenes/9.png)
- ![alt text](imagenes/10.png)
	  
  	- ##### 4.1.9 - Agregar a nuestro pipeline una nueva etapa que dependa de nuestra etapa de Construcción de Imagenes Docker y subida a ACR
	  - Agregar variables a nuestro pipeline:
  	  ```yaml
  	  ResourceGroupName: 'NOMBRE_GRUPO_RECURSOS' #Por ejemplo 'TPS_INGSOFT3_UCC'
	  backContainerInstanceNameQA: 'NOMBRE_CONTAINER_BACK_QA' #Por ejemplo 'as-crud-api-qa'
	  backImageTag: 'latest' 
	  container-cpu-api-qa: 1 #CPUS de nuestro container de QA
	  container-memory-api-qa: 1.5 #RAM de nuestro container de QA
  	  ```
		- ![alt text](imagenes/11.png)

  	  - Agregar variable secreta cnn-string-qa desde la GUI de ADO que apunte a nuestra BD de SQL Server de QA como se indica en el instructivo 5.3
  	    - ![alt text](imagenes/12.png)
		- ![alt text](imagenes/13.png)


  	  - Agregar tareas para crear un recurso Azure Container Instances que levante un contenedor con nuestra imagen de back
  	  ```yaml
  	  #----------------------------------------------------------
		### STAGE DEPLOY TO ACI QA
		#----------------------------------------------------------
		
		- stage: DeployToACIQA
		  displayName: 'Desplegar en Azure Container Instances (ACI) QA'
		  dependsOn: DockerBuildAndPush
		  jobs:
		    - job: deploy_to_aci_qa
		      displayName: 'Desplegar en Azure Container Instances (ACI) QA'
		      pool:
		        vmImage: 'ubuntu-latest'
		
		      steps:
		        #------------------------------------------------------
		        # DEPLOY DOCKER BACK IMAGE A AZURE CONTAINER INSTANCES QA
		        #------------------------------------------------------
		
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
		              echo "Connection String: $(cnn-string-qa)"
		          
		              az container delete --resource-group $(ResourceGroupName) --name $(backContainerInstanceNameQA) --yes
		
		              az container create --resource-group $(ResourceGroupName) \
		                --name $(backContainerInstanceNameQA) \
		                --image $(acrLoginServer)/$(backImageName):$(backImageTag) \
		                --registry-login-server $(acrLoginServer) \
		                --registry-username $(acrName) \
		                --registry-password $(az acr credential show --name $(acrName) --query "passwords[0].value" -o tsv) \
		                --dns-name-label $(backContainerInstanceNameQA) \
		                --ports 80 \
		                --environment-variables ConnectionStrings__DefaultConnection="$(cnn-string-qa)" \
		                --restart-policy Always \
		                --cpu $(container-cpu-api-qa) \
		                --memory $(container-memory-api-qa)
  	  ```

  	  - ##### 4.1.10 - Ejecutar el pipeline y en Azure Portal acceder al recurso de Azure Container Instances creado. Copiar la url del contenedor y navegarlo desde browser. Verificar que traiga datos.

	  - ![alt text](imagenes/14.png)
	  - ![alt text](imagenes/15.png)
	  - ![alt text](imagenes/16.png)
	  - ![alt text](imagenes/17.png)
	  - Link: http://fgemployeecrudapi-qa-back.brazilsouth.azurecontainer.io/api/Employee/getall 

  	  - ##### 4.1.11 - Agregar tareas para generar un recurso Azure Container Instances que levante un contenedor con nuestra imagen de front (DESAFIO)
  	  	- A la etapa creada en 4.1.9 Agregar tareas para generar contenedor en ACI con nuestra imagen de Front
  	        - Tener en cuenta que el contenedor debe recibir como variable de entorno API_URL el valor de una variable container-url-api-qa definida en nuestro pipeline.
  	        - Para que el punto anterior funcione el código fuente del front debe ser modificado para que la url de la API pueda ser cambiada luego de haber sido construída la imagen. Se deja un ejemplo de las modificaciones a realizar en el repo https://github.com/ingsoft3ucc/CrudAngularConEnvironment.git

	
		```yaml
	  #------------------------------------------------------
      # DEPLOY DOCKER FRONT IMAGE A AZURE CONTAINER INSTANCES QA
      #------------------------------------------------------

      - task: AzureCLI@2
        displayName: 'Desplegar Imagen Docker de Front en ACI QA'
        inputs:
          azureSubscription: '$(ConnectedServiceName)'
          scriptType: bash
          scriptLocation: inlineScript
          inlineScript: |
            echo "Resource Group: $(ResourceGroupName)"
            echo "Container Instance Name: $(frontContainerInstanceNameQA)"
            echo "ACR Login Server: $(acrLoginServer)"
            echo "Image Name: $(frontImageName)"
            echo "Image Tag: $(frontImageTag)"
            echo "Api Url: $(container-url-api-qa)"

            az container delete --resource-group $(ResourceGroupName) --name $(frontContainerInstanceNameQA) --yes

            az container create --resource-group $(ResourceGroupName) \
              --name $(frontContainerInstanceNameQA) \
              --image $(acrLoginServer)/$(frontImageName):$(frontImageTag) \
              --registry-login-server $(acrLoginServer) \
              --registry-username $(acrName) \
              --registry-password $(az acr credential show --name $(acrName) --query "passwords[0].value" -o tsv) \
              --dns-name-label $(frontContainerInstanceNameQA) \
              --ports 80 \
              --environment-variables API_URL="$(container-url-api-qa)" \
              --restart-policy Always \
              --cpu $(container-cpu-front-qa) \
              --memory $(container-memory-front-qa)
	```

	- ![alt text](imagenes/18.png)
	- ![alt text](imagenes/19.png)
	- ![alt text](imagenes/20.png)
	- ![alt text](imagenes/21.png)


  	- ##### 4.1.12 - Agregar tareas para correr pruebas de integración en el entorno de QA de Back y Front creado en ACI.
  	- ![alt text](imagenes/22.png) 
	- ![alt text](imagenes/23.png)


#### 4.4 Desafíos:
- 4.4.1 Agregar tareas para generar imagen Docker de Front. (Punto 4.1.8) (Listo)
- 4.4.2 Agregar tareas para generar en Azure Container Instances un contenedor de imagen Docker de Front. (Punto 4.1.11) (Listo)
- 4.4.3 Agregar tareas para correr pruebas de integración en el entorno de QA de Back y Front creado en ACI. (Punto 4.1.12) (Listo)
- 4.4.4 Agregar etapa que dependa de la etapa de Deploy en ACI QA y genere contenedores en ACI para entorno de PROD. (Listo)

- Debemos crear una nueva variable secreta en el Pipeline para que apunte a la BD de PROD, llamada: "cnn_string_prod"
	- ![alt text](imagenes/26.png)

- Otras variables a crear:
	- ![alt text](imagenes/27.png)

- ![alt text](imagenes/24.png) 
- ![alt text](imagenes/25.png)
- ![alt text](imagenes/28.png)

- Ahora tenemos los 2 nuevos contenedores de PROD:
	- ![alt text](imagenes/29.png)
	- ![alt text](imagenes/30.png)
	- ![alt text](imagenes/31.png)

- Vemos que tienen dentro el back y front funcionando:
	- ![alt text](imagenes/32.png)
	- ![alt text](imagenes/33.png)

### 5- Instructivos:

### 5.1 Crear un recurso Azure Container Registry

1\. Crear un nuevo recurso en nuestro grupo de recursos

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/b9a2ff13-8768-41a9-b94a-e051fdd7fb4d/ascreenshot.jpeg?tl_px=0,0&br_px=859,480&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=309,124)


2\. Type "azure container registry [[enter]]"


3\. Click en Crear Container registry

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/7a92bebc-a386-4455-b6ef-32e6d5f2c14a/ascreenshot.jpeg?tl_px=83,356&br_px=1066,905&force_format=jpeg&q=100&width=983&wat_scale=87&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=153,345)


4\. Click the "Nombre del Registro" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/9e737759-10f4-442d-b7c6-20fbbe7f83cd/ascreenshot.jpeg?tl_px=41,98&br_px=1417,867&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=243,276)


5\. Ingresar un nombre unico para nuestro ACR, como &lt;iniciales&gt;IngSoft3UCCACR


6\. En el combo Plan de Precios seleccionar "Básico"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/2aac180a-ae02-4e48-93fa-b2438069feef/ascreenshot.jpeg?tl_px=78,261&br_px=1225,902&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=250,405)


7\. Click "Siguiente: Redes >"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/a061f990-29b5-4cfd-8b57-9ead220e0f4b/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=295,600)


8\. Click "Siguiente: Cifrado >"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/12d0b212-01d9-4967-84b6-009d2e0e6a22/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=295,600)


9\. Click "Siguiente: Etiquetas >"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/b16d170c-d16c-4157-a07f-46353f789e8a/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=295,600)


10\. Click "Siguiente: Revisar y crear >"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/1aa47fd4-b7af-4098-890f-5f0cff1034a6/ascreenshot.jpeg?tl_px=39,145&br_px=1424,919&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=294,600)


11\. Click "Crear"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/9897db3f-a47a-45f5-8308-54b198446b9a/ascreenshot.jpeg?tl_px=0,4&br_px=1541,966&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=10,632)


12\. Click "Ir al recurso"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/c68f069c-652e-454c-9cc0-06cdbaee683e/ascreenshot.jpeg?tl_px=78,65&br_px=1225,706&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1,182)


13\. Copiar la url de nuestro recurso ACR

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/ce058583-1cf6-4e1f-9bb6-e18bb1b4eb35/ascreenshot.jpeg?tl_px=474,62&br_px=1457,611&force_format=jpeg&q=100&width=983&wat_scale=87&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=744,121)

### 5.2 Crear una Service Connection a Azure Resource Manager en Azure DevOps

1\. En nuestro proyecto de ADO, click "Project settings"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/e8c26f25-5725-448a-b0df-dd145827ecd4/ascreenshot.jpeg?tl_px=0,4&br_px=1541,966&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=30,653)


2\. Click "Service connections"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/ccbfd038-d76e-4a7d-9b6a-d815fd3f406e/ascreenshot.jpeg?tl_px=0,413&br_px=688,798&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=91,170)


3\. Click "New service connection"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/0ccbf282-43b0-47ba-a958-c4c1c02262f9/ascreenshot.jpeg?tl_px=852,0&br_px=1541,384&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=553,58)


4\. Click "Azure Resource Manager"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/16ef2054-72e5-4022-b20a-2ffd82d5b4ae/ascreenshot.jpeg?tl_px=852,18&br_px=1541,403&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=375,170)


5\. Click "Next"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/4a25944a-ae9b-40cb-b370-199c09a9e319/ascreenshot.jpeg?tl_px=852,583&br_px=1541,968&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=599,322)


6\. Dejar seleccionado el Authentication method por defecto y Click "Next"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/f174b447-e513-454d-a85d-9ffca08f5e11/ascreenshot.jpeg?tl_px=474,112&br_px=1457,661&force_format=jpeg&q=100&width=983&wat_scale=87&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=950,201)


7\. Ingresar nuestro mail de Azure Portal

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/490f06a3-cd69-42c7-aabc-32195ae98ab7/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=960,599&force_format=jpeg&q=100&width=1073&wat_scale=95&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=562,190)


8\. Click this button.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/b97bc19c-9792-4d6d-9cc5-c3163833b4ae/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=960,599&force_format=jpeg&q=100&width=1073&wat_scale=95&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=630,335)


9\. Ingresar contraseña de cuenta de Azure Portal y Click "Sign in"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/289ec148-a582-40e9-9229-8685ccad916f/ascreenshot.jpeg?tl_px=100,118&br_px=960,599&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=472,274)


10\. Seleccionar la suscripción y expandir el combo "Resource group"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/70f1fed3-5623-4704-a318-8f841e2ac412/ascreenshot.jpeg?tl_px=852,59&br_px=1541,444&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=476,170)


11\. Seleccionar el grupo de recursos donde creamos nuestro recurso de Azure Container Registry

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/f4ea71c0-9fc3-4863-9296-ce772c1c3cce/ascreenshot.jpeg?tl_px=852,141&br_px=1541,526&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=416,170)


12\. Colocarle un nombre a la Service Connection

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/b79d354e-5348-4e9f-979a-e2a0becacb89/ascreenshot.jpeg?tl_px=852,134&br_px=1541,519&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=417,170)


13\. Type "ServiceConnectionARM"


14\. Click "Grant access permission to all pipelines"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/0fbd6fd9-e144-4f92-bc74-29d97a6d5fba/ascreenshot.jpeg?tl_px=852,319&br_px=1541,704&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=452,170)


15\. Click "Save"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/5b693682-d1a3-4fab-b265-20f2903a2bd6/ascreenshot.jpeg?tl_px=852,355&br_px=1541,740&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=600,170)

### 5.3  Configurar variables secretas en Azure DevOps

1\. En nuestro pipeline Click "Variables"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/d7638d53-1aea-4dc0-8c3d-1821f0cd7cb5/ascreenshot.jpeg?tl_px=852,0&br_px=1541,384&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=516,52)


2\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/d2dbe776-5623-4662-9d39-adcc6b85b690/ascreenshot.jpeg?tl_px=852,0&br_px=1541,384&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=611,58)


3\. Type "cnn_string_qa"


4\. Click this text field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/cae06e4c-dcb6-462f-a0dd-f682de8557c4/ascreenshot.jpeg?tl_px=852,0&br_px=1541,384&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=452,134)


5\. Ingresar la cadena de conexión de nuestra BD de SQL Server


6\. Click "Keep this value secret"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/54d4f8d8-740c-407f-9135-fdab8f680639/ascreenshot.jpeg?tl_px=852,0&br_px=1541,384&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=376,159)


7\. Click "OK"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/e706649e-16c2-4da9-82bb-077b3b05d007/ascreenshot.jpeg?tl_px=0,4&br_px=1541,966&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1054,634)


8\. Click "Save"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-22/23a4eb59-095f-4c76-b0cd-6873b89935bb/ascreenshot.jpeg?tl_px=852,583&br_px=1541,968&force_format=jpeg&q=100&width=688&wat_scale=61&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=616,324)




### 6-  Presentación del trabajo práctico.
- Subir un doc al repo de GitHub con las capturas de pantalla de los pasos realizados. Debe ser un documento (md, word, o pdf), no videos. Y el documento debe seguir los pasos indicados en el Desarrollo del TP.
- Acceso al repo de Azure Devops para revisar el trabajo realizado.

### 7-  Criterio de Calificación
Los pasos 4.1 al 4.X representan un 60% de la nota total, los pasos 4.X y subsiguientes representan el 40% restante.

### Notas:
- Se trabajo sobre el Pipeline del TP7, Repo "TP7_AngularAPI" y use Grupo de Recursos: "TP05IngSoft3UCC"





