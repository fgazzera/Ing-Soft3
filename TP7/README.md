### 4- Desarrollo:
#### Prerequisitos:

#### 4.1 Agregar Code Coverage a nuestras pruebas unitarias de backend y front-end e integrarlas junto con sus resultados en nuestro pipeline de build.
- Desarrollo del punto 4.1: 
	- ##### 4.1.1 En el directorio raiz de nuestro proyecto Angular instalar el siguiente paquete:
		- ![alt text](imagenes/1.png)*+/

   	 - ##### 4.1.2 Editar nuestro archivo karma.conf.js para que incluya reporte de cobertura
  		- ![alt text](imagenes/2.png)


   	- ##### 4.1.3 En el dir raiz del proyecto EmployeeCrudApi.Tests ejecutar:
   	  ```bash
   	  dotnet add package coverlet.collector
   	  ```
		- ![alt text](imagenes/3.png)

   	- ##### 4.1.4 Agregar a nuestro pipeline ANTES del Build de Back la tarea de test con los argumentos especificados y la de publicación de resultados de cobertura:
	```yaml
    - task: DotNetCoreCLI@2
      displayName: 'Ejecutar pruebas de la API'
      inputs:
        command: 'test'
        projects: '**/*.Tests.csproj'  # Asegúrate de que el patrón apunte a tu proyecto de pruebas
        arguments: '--collect:"XPlat Code Coverage"'

    - task: PublishCodeCoverageResults@2
      inputs:
        summaryFileLocation: '$(Agent.TempDirectory)/**/*.cobertura.xml'
        failIfCoverageEmpty: false
      displayName: 'Publicar resultados de code coverage del back-end'
	```
	
	- ![alt text](imagenes/4.png)

	- ##### 4.1.5 Agregar a nuestro pipeline ANTES del Build de front la tarea de test y la de publicación de los resultados.
	   ```yaml
	    - script: npx ng test --karma-config=karma.conf.js --watch=false --browsers ChromeHeadless --code-coverage
	      displayName: 'Ejecutar pruebas del front'
	      workingDirectory: $(projectPath)
	      continueOnError: true  # Para que el pipeline continúe aunque falle
	    
	    - task: PublishCodeCoverageResults@2
	      inputs:
	        summaryFileLocation: '$(projectPath)/coverage/lcov.info'
	        failIfCoverageEmpty: false
	      condition: always()  # Esto asegura que se ejecute siempre
	      displayName: 'Publicar resultados de code coverage del front'  
	    
	    - task: PublishTestResults@2
	      inputs:
	        testResultsFormat: 'JUnit'
	        testResultsFiles: '$(projectPath)/test-results/test-results.xml'
	        failTaskOnFailedTests: true
	      condition: always()  # Esto asegura que se ejecute siempre
	      displayName: 'Publicar resultados de pruebas unitarias del front'
    	```
	
	- ![alt text](imagenes/5.png)
	
	- ##### 4.1.6 Ejecutar el pipeline y analizar el resultado de las pruebas unitarias y la cobertura de código.
		- ![alt text](imagenes/6.png)
		- ![alt text](imagenes/7.png)


#### 4.2 Agregar Análisis Estático de Código con SonarCloud:

SonarCloud https://www.sonarsource.com/products/sonarcloud/ es una plataforma de análisis de calidad de código basada en la nube que ayuda a los equipos de desarrollo a detectar errores, vulnerabilidades de seguridad, malas prácticas y problemas de mantenibilidad en el código. Proporciona análisis estático de código para múltiples lenguajes, como Java, .NET, JavaScript, TypeScript, Python, y más, ofreciendo métricas detalladas sobre la cobertura de pruebas, duplicación de código, complejidad ciclomatica, y deuda técnica. Además, SonarCloud se integra fácilmente con sistemas de CI/CD y plataformas de control de versiones como GitHub, Azure DevOps y Bitbucket, lo que permite un análisis continuo del código en cada commit o despliegue.

La plataforma destaca por ofrecer un tablero interactivo donde se pueden visualizar y priorizar los problemas detectados, facilitando la refactorización y la mejora de la calidad del código. SonarCloud permite a los equipos identificar vulnerabilidades y puntos críticos de seguridad, además de generar informes de cobertura de pruebas automatizadas, ayudando a mantener el código limpio, seguro y fácil de mantener a lo largo del ciclo de vida del desarrollo.

- ### Resumen de Métricas de Calidad de Código en SonarCloud

	1. **Coverage (Cobertura de Código)**: Mide el porcentaje de código cubierto por pruebas automatizadas.
	2. **Bugs (Errores)**: Identifica problemas que podrían causar fallos en el código en tiempo de ejecución.
	3. **Vulnerabilities (Vulnerabilidades)**: Detecta posibles puntos de riesgo que podrían comprometer la seguridad del código.
	4. **Code Smells**: Indica malas prácticas o patrones de código que, aunque no causan fallos, afectan la calidad y mantenibilidad.
	5. **Duplications (Duplicación de Código)**: Mide el porcentaje de código duplicado en el proyecto, lo que afecta la mantenibilidad.
	6. **Maintainability (Mantenibilidad)**: Evalúa la facilidad con la que el código puede ser modificado o extendido sin introducir nuevos errores.
	7. **Technical Debt (Deuda Técnica)**: Estima el tiempo necesario para corregir todos los problemas de calidad del código.
	8. **Reliability (Fiabilidad)**: Mide la probabilidad de que el código funcione sin errores.
	9. **Security Hotspots (Puntos Críticos de Seguridad)**: Indica áreas del código que deben ser revisadas manualmente por posibles problemas de seguridad.
	10. **Cyclomatic Complexity (Complejidad Ciclomática)**: Mide el número de caminos lógicos independientes en el código, indicando su complejidad estructural.
	11. **Cognitive Complexity (Complejidad Cognitiva)**: Evalúa la dificultad de entender el código, considerando aspectos de legibilidad y comprensión.
	12. **Maintainability Rating**: Clasifica el proyecto en una escala de A a E según la deuda técnica y la facilidad de mantenimiento.
	13. **Branch Coverage**: Mide la cobertura de las ramas de decisión en el código (por ejemplo, en estructuras `if`, `else` o `switch`).
	14. **Complexity per Function**: Mide la complejidad promedio de las funciones dentro del proyecto.
	15. **Complexity per Class**: Mide la complejidad promedio por clase, ayudando a evaluar la arquitectura y el diseño del sistema.
	16. **Lines of Code (LoC)**: Cuenta el número total de líneas de código en el proyecto, excluyendo comentarios y espacios en blanco.
	17. **Comment Density (Densidad de Comentarios)**: Mide el porcentaje de líneas de código que son comentarios, útil para evaluar la documentación interna.
	18. **Issues (Problemas)**: Cuenta el número total de problemas detectados en el código, que pueden clasificarse como bugs, vulnerabilidades o code smells.
	19. **Security Rating**: Proporciona una clasificación general de la seguridad del código, en una escala de A (mejor) a E (peor).
	20. **Reliability Rating**: Clasifica la confiabilidad del código en una escala de A a E, basada en la cantidad y gravedad de los errores detectados.
	21. **Security Remediation Effort**: Estima el tiempo necesario para resolver todos los problemas de seguridad identificados en el código.
	22. **Uncovered Conditions**: Mide el número de condiciones lógicas que no están cubiertas por las pruebas automatizadas.
	23. **Duplicated Blocks**: Cuenta los bloques de código que se repiten en el proyecto.
	24. **Unresolved Issues**: Indica el número de problemas detectados que aún no han sido resueltos.
	
- Desarrollo del punto 4.2: Demostración de cómo integrar SonarCloud en un pipeline de CI/CD y cómo leer los reportes de análisis estático.
	- ##### 4.2.1 Integraremos SonarCloud para analizar el código fuente. Configurar SonarCloud en nuestro pipeline siguiendo instructivo 5.1
	  - Antes de nuestra tarea de Build del Back:
		- ![alt text](imagenes/8.png)

  	  - Despues de nuestra tarea de Build del Back:
		- ![alt text](imagenes/9.png)

  	- ##### 4.2.2 Vemos el resultado de nuestro pipeline, en extensions tenemos un link al análisis realizado por SonarCloud
		- ![alt text](imagenes/10.png)
  
	- ##### 4.2.3 Ir al link y analizar toda la información obtenida. Detallar en la entrega del TP los puntos más relevantes del informe, qué significan y para qué sirven.
		- ![alt text](imagenes/11.png)
		- ![alt text](imagenes/12.png)

		- El informe de SonarCloud presenta los siguientes puntos relevantes relacionados con la calidad y seguridad del código:
		
		1. Security Hotspots (Puntos críticos de seguridad):

			Se señala un posible riesgo de credenciales ("password" detectado en un archivo de configuración JSON). SonarCloud detecta esto como un riesgo de seguridad potencial, ya que almacenar credenciales sensibles directamente en el código podría exponer el sistema a ataques. La recomendación es no almacenar contraseñas directamente en archivos de configuración.
			Esto se debe a que tenemos expuesto en "EmployeeCrudApi/EmployeeCrudApi/appsettings.json" el password de la bd.

		2. Issues (Problemas de código):

			Se detectan Code Smells o problemas de calidad en el código, como el uso de Await AnyAsync en lugar de un patrón más eficiente para operaciones asíncronas, lo que impacta en la confiabilidad del código. Este tipo de problema puede generar errores difíciles de identificar y corregir en tiempo de ejecución.

			Otro problema de Code Smell es que ciertos métodos deberían definirse como métodos estáticos (por ejemplo, ValidateEmployee). Definir estos métodos como estáticos mejora la mantenibilidad del código al clarificar su intención y reducir el uso de recursos.
		
		3. Prioridades:

			SonarCloud clasifica los problemas por prioridad: alta, media y baja. Los problemas de alta prioridad suelen estar relacionados con vulnerabilidades de seguridad o problemas que pueden causar fallos críticos en el sistema.
			Los problemas de baja prioridad (como problemas menores de configuración o inconsistencias de código) no necesitan resolverse de inmediato, pero a largo plazo pueden contribuir a reducir la calidad del software.

		4. Tiempo estimado para corregir problemas:

			SonarCloud nos da una estimacion del tiempo que nos tomaria corregir los problemas detectados.
			El tiempo se estima analizando cada issue, basado en su complejidad y el tipo de problema.


#### 4.3 Pruebas de Integración con Cypress:

- Desarrollo del punto 4.3: 
	- ##### 4.3.1 En el directorio raiz de nuestro proyecto Angular instalar el siguiente paquete:
  	```bash
	npm install cypress --save-dev
   	```
- ![alt text](imagenes/13.png)

   	- ##### 4.3.2 Abrir Cypress:
  	```bash
   	npx cypress open
   	```
- ![alt text](imagenes/14.png)

	- ##### 4.3.3 Inicializar Cypress en nuestro proyecto como se indica en el instructivo 5.2
	- Seguimos el instructivo 5.2
		- ![alt text](imagenes/15.png)
		- ![alt text](imagenes/16.png)
		- ![alt text](imagenes/17.png)
		- ![alt text](imagenes/18.png)
		- ![alt text](imagenes/19.png)
		- ![alt text](imagenes/20.png)
		- ![alt text](imagenes/21.png)
		-  Ahora vemos que dentro de nuestro proyecto se creo automaticamente una carpeta llamada "cypress" con otras 4 dentro.
		- ![alt text](imagenes/22.png)


  	- ##### 4.3.4 Crear nuestra primera prueba navegando a nuestro front.
 	En la carpeta cypress/e2e, crear un archivo con el nombre primer_test.js y agregar el siguiente código para probar la página de inicio de nuestro front:
  	```js
	  describe('Mi primera prueba', () => {
	  it('Carga correctamente la página de ejemplo', () => {
	    cy.visit('https://as-crud-web-api-qa.azurewebsites.net/') // Colocar la url local o de Azure de nuestro front
	    cy.get('h1').should('contain', 'EmployeeCrudAngular') // Verifica que el título contenga "EmployeeCrudAngular"
	  })
	})
   	```
    - ![alt text](imagenes/23.png)

	- ##### 4.3.5 Correr nuestra primera prueba
 	Si está abierta la interfaz gráfica de Cypress, aparecerá el archivo primer_test.cy.js en la lista de pruebas. Clic en el archivo para ejecutar la prueba.
	- ![alt text](imagenes/24.png)
	- ![alt text](imagenes/25.png)
	
	También es posible ejecutar Cypress en modo "headless" (sin interfaz gráfica) utilizando el siguiente comando:
	```bash
 	npx cypress run
 	```
	- ![alt text](imagenes/26.png)

	- ##### 4.3.6 Modificar nuestra prueba para que falle.
	  - Editamos el archivo primer_test.cy.js y hacemos que espere otra cosa en el título
	  - ![alt text](imagenes/27.png)
	  - Ejecutamos cypress en modo headless
	  - ![alt text](imagenes/28.png)

	Cypress captura automáticamente pantallas cuando una prueba falla. Las capturas de pantalla se guardan en la carpeta `cypress/screenshots`.
	- ![alt text](imagenes/29.png)

 	- ##### 4.3.6 Grabar nuestras pruebas para que Cypress genere código automático y genere reportes:
    	 - Cerramos Cypress
	 - Editamos el archivo cypress.config.ts incluyendo la propiedad **experimentalStudio** en true y la configuración de reportería.
	- ![alt text](imagenes/30.png)


       - Corremos nuevamente Cypress con npx cypress open, una vez que se ejecute nuestra prueba tendremos la opción de "Add Commands to Test". Esto permitirá interactuar con la aplicación y generar automáticamente comandos de prueba basados en las interacciones con la página:
         
		- ![alt text](imagenes/31.png)

       - Por ejemplo, si agregamos un nuevo empleado y luego verificamos que esté en la lista, Cypress nos generará un código como este:
 	```typescript
  	describe('Mi primera prueba', () => {
	  it('Carga correctamente la página de ejemplo', () => {
        cy.visit('http://localhost:4200/') // Colocar la url local o de Azure de nuestro front
        cy.get('h1').should('contain', 'EmployeeCrudAngular') // Verifica que el título contenga "EmployeeCrudAngular"
			/* ==== Generated with Cypress Studio ==== */
		cy.get('.form-control').click();
		cy.get('.btn').click();
		cy.get('.form-control').clear('I');
		cy.get('.form-control').type('Facundo Pedro');
		cy.get('.btn').click();
		cy.get(':nth-child(11) > :nth-child(3)').click();
		/* ==== End Cypress Studio ==== */
      })
	})
 	```
- ![alt text](imagenes/32.png)


  	- Por supuesto que habrá que hacerle ajustes, como por ejemplo que se fije siempre en la última fila de la grilla y no en la posición 15 como lo grabó, es ahí cuando consultando la documentación de Cypress debemos ver cómo modificar el código, en nuestro caso de ejemplo sería así:
  	  ```typescript
  	  cy.get('tr:last-child > :nth-child(2)').should('have.text', ' Facundo Pedro ');
  	  ```
  	- ##### 4.3.7 Hacemos prueba de editar un empleado
    	 - Creamos en cypress/e2e/ un archivo editEmployee_test.cy.js con el siguiente contenido, guardamos y aparecerá en Cypress:
  	   		- ![alt text](imagenes/33.png)
			- ![alt text](imagenes/34.png)
			- ![alt text](imagenes/35.png)
  	

	- Hacemos "Add command to the test" y empezamos a interactuar con la página
	-  ![alt text](imagenes/36.png)

  	- Hacemos algunos ajustes al código generado:
	- ![alt text](imagenes/37.png)
	- ![alt text](imagenes/38.png)

#### 4.4 Desafíos:
- Integrar en el pipeline SonarCloud para nuestro proyecto Angular, mostrar el resultado obtenido en SonarCloud
- Implementar en Cypress pruebas de integración que incluya los casos desarrollados como pruebas unitarias del front en el TP06.
- Incorporar al pipeline de Deploy la ejecución de las pruebas de integración y la visualización de sus resultados.
- **Resultado esperado**:
    - Un Pipeline en YAML que incluya a) Build de QA y Front con ejecución y resultado de pruebas de code coverage, pruebas unitarias y análisis de Sonar Cloud y b) Deploy a WebApp(s) de QA y Front que incluya ejecución y resultado de pruebas de integración

    <img width="1318" alt="image" src="https://github.com/user-attachments/assets/76324d85-3706-495d-ad56-6054371a3019">

    - Dos Stages: Una para Build, Test Unitarios, Code Coverage y SonarCloud y otra para el Deploy a QA con Tests de Integración

	<img width="296" alt="image" src="https://github.com/user-attachments/assets/03a69736-a1c1-4ce2-a648-4237da015150">

    - En la pestaña Test, poder visualizar los Test Unitarios de Front y Back y los Test de Integracion:

	<img width="1262" alt="image" src="https://github.com/user-attachments/assets/83fe8613-631d-4c4d-9042-37455701c0e3">

    - En la pestaña Code Coverage, visualizar la cobertura de las pruebas unitarias de Back y de Front:

	<img width="1199" alt="image" src="https://github.com/user-attachments/assets/e3d48d54-945c-4b99-afbe-6a72b5f95c3f">

    - En la pestaña Extensions, ver el análisis de SonarCloud en verde

       <img width="720" alt="image" src="https://github.com/user-attachments/assets/c5511fb6-3d39-4b9c-ad9a-e688bdaa3016">
    
    - Un documento de una carilla explicando qué información pudieron sacar del análisis de Sonar Cloud y de las pruebas de cobertura.

### 5- Instructivos:
### 5.1 Integrate SonarCloud with Azure DevOps Pipelines

1\. Navigate to [https://www.sonarsource.com/products/sonarcloud/](https://www.sonarsource.com/products/sonarcloud/)


2\. Click "SIGN UP"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/2106a375-797f-4285-9b21-28df5c759eb4/ascreenshot.jpeg?tl_px=0,1&br_px=1541,963&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=807,93)


3\. Click "AZURE DEVOPS"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/686ea83e-702d-461f-94b9-f102c795e7b9/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=289,372)


4\. Seleccionar la cuenta con la que ingresan a Azure Devops

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/38bfaa76-241c-42e8-8a01-1216aa0961a0/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=498,348)


5\. Click "Import an organization"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/bd118a24-7d17-4655-9a32-4205156c297c/ascreenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=340,427)


6\. Ingresar el nombre de su organización en ADO. Por ejemplo si la url es <https://dev.azure.com/ingsoft3ucc/> debemos ingresar ingsoft3ucc

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/ddb28ade-eb54-4ff4-9cb9-4557eba0e96c/ascreenshot.jpeg?tl_px=0,1&br_px=1541,963&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=418,228)


7\. Creamos un PAT en ADO desde "Projects - Home"


8\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d4005004-242b-4c78-8e7f-874fbbf3d0f0/ascreenshot.jpeg?tl_px=99,46&br_px=1506,832&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1059,-57)


9\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/53eab93f-e876-44a1-80ec-90c7183f0eb1/ascreenshot.jpeg?tl_px=767,89&br_px=1541,522&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=563,191)


10\. Click "New Token"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/5e395327-11aa-4b2d-92b1-f6cf43d4a3ba/ascreenshot.jpeg?tl_px=457,63&br_px=1456,621&force_format=jpeg&q=100&width=999&wat_scale=89&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=942,-14)


11\. Darle un nombre, seleccionar nuestra organización, darle duración máxima de un año, scope "Full Access" y "Create"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/dc9dc427-a287-411b-a2be-873425b32e12/ascreenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=702,630)


12\. Copiar el token

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/bf06bfb0-b42c-4ec3-a6e9-0359883452ec/ascreenshot.jpeg?tl_px=608,49&br_px=1472,531&force_format=jpeg&q=100&width=864&wat_scale=77&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=661,69)


13\. Click "Close"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b90c5251-6d06-4fea-b69a-c1bdff1fbfea/ascreenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1040,633)


14\. Ingresar el token copiado y dar click en "Continue"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/819c66c7-600c-4bc6-93bf-ca4e5e824568/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=167,364)


15\. Double-click "ingsoft3ucc"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/420ee9d9-f3fc-4cef-ad90-b04fb36ee470/ascreenshot.jpeg?tl_px=41,128&br_px=1417,897&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=224,300)


16\. Press [[meta]] + [[c]]


17\. Poner como nombre el nombre de nuestra organizacion en ADO.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/5e3393c0-9aeb-42ff-8a6c-eec57679fdf0/ascreenshot.jpeg?tl_px=0,379&br_px=859,860&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=288,212)


18\. Click "Add additional info"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/ef46f5a8-937b-4954-a863-d855059456d1/ascreenshot.jpeg?tl_px=78,248&br_px=1225,889&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=231,332)


19\. Click "Select Free"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/beb8b145-d809-4ace-9124-00b9834df8ab/ascreenshot.jpeg?tl_px=0,383&br_px=859,864&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=293,212)


20\. Click "Create Organization"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/76df17cc-9dae-4d70-a2b6-5d1f855b097b/ascreenshot.jpeg?tl_px=0,487&br_px=859,968&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=294,266)


21\. Vemos que la cuenta Free no nos permite trabajar con proyectos privados. Si sus proyectos son privados porque pidieron acceso a pipelines privadas en lugar de publicas. Deben hacer un upgrade de plan que es gratis por 14 días. RECUERDEN DARSE DE BAJA ANTES DE LOS 14 DIAS PARA EVITAR COSTOS.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/a2c38e2e-d9d4-4999-98a1-ab94fa813ce0/ascreenshot.jpeg?tl_px=41,49&br_px=1417,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=144,185)


22\. Click "14-day free trial"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/4a334853-9315-4530-bbdd-1ffd2cce3f8d/ascreenshot.jpeg?tl_px=634,102&br_px=1494,583&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)


23\. Click "Upgrade ingsoft3ucc"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/fd6cba4a-5845-4ba0-b21f-aa77abb20e54/ascreenshot.jpeg?tl_px=681,367&br_px=1541,848&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=423,212)


24\. Click the "How many lines of code?*" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/552702e8-e019-4810-8825-562269408f79/ascreenshot.jpeg?tl_px=41,123&br_px=1417,892&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=438,296)


25\. Click "Continue to billing information"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/8d4cbcd7-824a-41b0-aa22-8c926661ecc2/ascreenshot.jpeg?tl_px=0,372&br_px=859,853&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=314,212)


26\. Click "Personal"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/4d71db69-30d0-4401-8472-21a725f85620/ascreenshot.jpeg?tl_px=78,261&br_px=1225,902&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=65,392)


27\. Click "Continue to payment information"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/681ae810-0f60-42f1-8fdb-bfd946d57ec6/ascreenshot.jpeg?tl_px=0,487&br_px=859,968&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=318,242)


28\. Click the "Credit Card Number" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/64905673-d1f8-4ce0-9e0f-271b43ca1a09/ascreenshot.jpeg?tl_px=0,487&br_px=859,968&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=261,352)


29\. Completar los datos de la tarjeta

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/723cf7b8-439f-49e2-bc65-e8e9fb3d33e0/ascreenshot.jpeg?tl_px=83,356&br_px=1066,905&force_format=jpeg&q=100&width=983&wat_scale=87&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=173,479)


30\. Click "Upgrade"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/80881efb-e217-48cc-9ddb-0613ad7e63e9/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=95,572)


31\. Click "Projects"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/fc6a1b38-7be4-42e0-94ca-dc91e084cdf6/ascreenshot.jpeg?tl_px=41,49&br_px=1417,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=-13,40)


32\. Click "Analyze a new project"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/9184ac9e-8900-4a2b-b5e2-24fc0d9f8c44/ascreenshot.jpeg?tl_px=0,4&br_px=1541,966&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=507,401)


33\. Seleccionamos el proyecto que queremos analizar

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/846ace02-6fc0-4b62-a3b9-cef3b9668306/ascreenshot.jpeg?tl_px=0,211&br_px=859,692&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=240,212)


34\. Click "Set Up"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/36241179-592f-4624-9e1d-3948f6684302/ascreenshot.jpeg?tl_px=123,49&br_px=1499,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=730,207)


35\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/35cfff85-66c4-412d-97a0-da999d10deef/ascreenshot.jpeg?tl_px=116,124&br_px=1492,893&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=551,297)


36\. Click "Create project"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/e8be24c2-3379-4ee0-b01b-a22726047654/ascreenshot.jpeg?tl_px=41,149&br_px=1417,918&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=259,557)


37\. Click "Close"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/f09bcee3-4659-46d6-b18f-fe9dd9117d8d/ascreenshot.jpeg?tl_px=123,49&br_px=1499,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1009,24)


38\. Click "With Azure DevOps Pipelines"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/8d366181-c14a-40b3-938e-7dee04e6bf57/ascreenshot.jpeg?tl_px=78,65&br_px=1225,706&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=386,106)


39\. Click "SonarCloud extension"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/6728e9b7-a919-4d68-afb6-b8be41d04567/ascreenshot.jpeg?tl_px=78,65&br_px=1225,706&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=390,167)


40\. Switch to tab "SonarCloud - Visual Studio Marketplace"


41\. Click "Get it free"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/77cd8c0f-692f-4498-bd2e-284eec7ef73f/ascreenshot.jpeg?tl_px=12,8&br_px=872,489&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)


42\. Seleccionar nuestra cuenta de ADO

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b6263cd1-9f4d-46e0-92ae-1f4f1347965f/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=477,246)


43\. Click "Go to Marketplace"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/1157da3b-cebd-4fc1-a20d-362cfb412d17/ascreenshot.jpeg?tl_px=41,49&br_px=1417,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=419,211)


44\. Click "Organization settings"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/1bbebaa7-27e9-4d5f-8e46-efda009fa613/ascreenshot.jpeg?tl_px=34,135&br_px=1441,921&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=-8,605)


45\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/61a8f1b2-247f-433c-9b6f-48f2f2f814d3/ascreenshot.jpeg?tl_px=76,99&br_px=1244,752&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=-3,238)


46\. Verificar que se instaló correctamente la extensión

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/1ecd8107-94a1-4e95-81ac-1ed7d93f8d63/screenshot.jpeg?tl_px=0,0&br_px=808,415&force_format=jpeg&q=100)


47\. Seguir las instrucciones para agregar un Service Endpoint en ADO

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/3dcfe7a2-644a-440e-aa78-c2e3510f4889/ascreenshot.jpeg?tl_px=225,302&br_px=1085,783&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)


48\. Switch to tab "Projects - Home"


49\. Click en el proyecto donde queremos integrar SonarCloud

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/51abf6f1-723a-471d-b134-811714fd6bcd/ascreenshot.jpeg?tl_px=34,46&br_px=1441,832&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=333,232)


50\. Click "Project settings"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/3480011a-9d69-4e93-9b52-60239db38616/ascreenshot.jpeg?tl_px=84,346&br_px=1083,904&force_format=jpeg&q=100&width=999&wat_scale=89&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=-20,563)


51\. Click "Service connections"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/0e517b77-759d-4e6b-8829-6fcf6676f5ef/ascreenshot.jpeg?tl_px=0,458&br_px=773,891&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=130,191)


52\. Click "New service connection"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/a81ab54a-5777-49be-93ec-347014f2b53b/ascreenshot.jpeg?tl_px=99,46&br_px=1506,832&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=972,-8)


53\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/4ea1330d-ae49-4370-949e-24e88dece400/ascreenshot.jpeg?tl_px=608,436&br_px=1472,918&force_format=jpeg&q=100&width=864&wat_scale=77&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=485,280)


54\. Click "Next"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/bd1a0099-4811-4f05-89f3-c069ef98984f/ascreenshot.jpeg?tl_px=767,535&br_px=1541,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=667,360)


55\. Double-click this password field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/5dcab52b-7a36-4263-bcbc-f7b446579de1/ascreenshot.jpeg?tl_px=767,0&br_px=1541,432&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=449,118)


56\. Desde SonarCloud:

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/04f14466-53b7-4130-83f0-fadb58ad2b9a/ascreenshot.jpeg?tl_px=123,49&br_px=1499,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1012,23)


57\. Click "Security"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/f0073997-93b4-4dad-95ff-79725e4ed9ea/ascreenshot.jpeg?tl_px=0,0&br_px=859,480&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=130,117)


58\. Click the "Enter Token Name" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/a3bfe0a6-9ced-4274-bfd4-f6d6206f768e/ascreenshot.jpeg?tl_px=0,166&br_px=859,647&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=375,212)


59\. Ingresamos el nombre de nuestro proyecto en ADO


60\. Click "Generate Token"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/596722d7-69ce-466d-9e51-586603af86d0/ascreenshot.jpeg?tl_px=217,114&br_px=1364,755&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=537,244)


61\. Copiamos el token

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d1863435-2afc-46a9-8db8-3442ce13f066/ascreenshot.jpeg?tl_px=239,277&br_px=1099,758&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)


62\. Pagamos el Token y le damos Verify

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b55d6081-a611-4b2d-901e-6846a709004b/ascreenshot.jpeg?tl_px=764,96&br_px=1537,529&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=362,191)


63\. Type "SonarCloud"


64\. Click "Grant access permission to all pipelines"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/1b125d7d-fe71-4cb9-aa35-040cf11432ce/ascreenshot.jpeg?tl_px=767,241&br_px=1541,674&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=370,191)


65\. Click "Verify and save"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b6a2f242-671f-4218-84d6-a4175499fffd/ascreenshot.jpeg?tl_px=767,279&br_px=1541,712&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=591,191)


66\. Seguimos las instrucciones para configurar nuestro pipeline de .NET

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/c95be260-0f84-477a-ae34-d2fd17a0e6c2/ascreenshot.jpeg?tl_px=68,487&br_px=928,968&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,308)


67\. Click "Copy"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/61e5d5da-1678-45cc-a63e-cafce4401c0e/ascreenshot.jpeg?tl_px=123,49&br_px=1499,818&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=1009,201)


68\. Vamos a los pipelines de nuestro proyecto

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/cf924b36-ff1d-4f81-aac0-24b6cb6cef40/ascreenshot.jpeg?tl_px=0,89&br_px=773,522&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=71,191)


69\. Seleccionamos nuestro pipeline

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d957e6a6-4b02-49f1-a736-737b591c07c3/ascreenshot.jpeg?tl_px=99,46&br_px=1506,832&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=651,141)


70\. Click "Edit"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/fc17d4f0-eddc-4641-a02c-a61dc9cb371b/ascreenshot.jpeg?tl_px=296,64&br_px=1464,717&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=956,-19)


71\. Pegamos lo copiado

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/968f3599-a14d-4346-9b29-4008005490d2/ascreenshot.jpeg?tl_px=0,112&br_px=773,545&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=294,191)


72\. Editamos porque steps esta repetido y corregimos indentación

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/af56c6a0-0089-4c09-a582-ecd91ecb591d/ascreenshot.jpeg?tl_px=131,131&br_px=904,564&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=362,191)


73\. Colocaremos una tarea de SonarCloud ANTES de nuestra tarea de Build

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/e540259f-fb3d-485a-920f-e84dbee4f60d/ascreenshot.jpeg?tl_px=312,263&br_px=1085,696&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=362,191)


74\. Click the "Search tasks" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/a0aa35fe-d209-4dd1-8747-5df9a07affb0/ascreenshot.jpeg?tl_px=767,0&br_px=1541,432&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=573,154)


75\. Type "sonar"


76\. Click "Prepare Analysis Configuration"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/e44ed826-4b09-4071-80b2-9ba6aa7f52b8/ascreenshot.jpeg?tl_px=767,16&br_px=1541,449&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=591,191)


77\. Click this button field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/79097b84-79bc-4e6d-92d1-7c86358ce999/ascreenshot.jpeg?tl_px=767,8&br_px=1541,441&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=586,191)


78\. Click "SonarCloud"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/ff9227d6-d67b-4f6f-8290-7f2927b2e0cf/ascreenshot.jpeg?tl_px=767,34&br_px=1541,467&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=564,191)


79\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d50d902a-1828-4208-8db9-5493cf45d2b9/ascreenshot.jpeg?tl_px=767,70&br_px=1541,503&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=690,191)


80\. Click "ingsoft3ucc (ingsoft3ucc)"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d338d135-2c15-4aa7-8068-7124e16c4ac2/ascreenshot.jpeg?tl_px=767,98&br_px=1541,531&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=611,191)


81\. En SonarCloud copiamos el Project Key y Project Name que nos pide

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/4bd2dc8b-d1fa-4ba3-8ce7-2a530fbed134/ascreenshot.jpeg?tl_px=325,139&br_px=1701,908&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=567,309)


82\. Switch to tab "Angular_WebAPINetCore8_CRUD_Sample - Pipelines"


83\. Click this field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/15b87524-ff7d-4531-8638-17d2211bb140/ascreenshot.jpeg?tl_px=767,309&br_px=1541,742&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=512,191)


84\. Press [[meta]] + [[v]]


85\. Click this button.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b852fa50-1105-4bb5-8819-c65d33d76c9f/ascreenshot.jpeg?tl_px=555,386&br_px=1415,867&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)


86\. Switch to tab "Angular_WebAPINetCore8_CRUD_Sample - Pipelines"


87\. Click this field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/e84bbc4d-1452-4c9f-92a1-92f21c5d1e68/ascreenshot.jpeg?tl_px=767,377&br_px=1541,810&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=491,191)


88\. Press [[meta]] + [[v]]


89\. Click "Add"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b3813f8e-fbd7-49fb-b526-a10dab6bda91/ascreenshot.jpeg?tl_px=767,535&br_px=1541,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=672,377)


90\. Type " [[tab]]  [[tab]]"


91\. Corregimos indentacion

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/782b4c9f-43c3-4ba2-a1ae-0cae3a2ab6f3/ascreenshot.jpeg?tl_px=76,250&br_px=1244,903&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=357,452)


92\. Agregamos otra tarea de SonarCloud DESPUES de nuestra tarea de Build. Click the "Search tasks" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/e2b88acb-9fb8-4c1a-9e62-c2c70a580b93/ascreenshot.jpeg?tl_px=99,46&br_px=1506,832&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=943,82)


93\. Type "Sonar"


94\. Click "Run Code Analysis"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/6feb354a-5da8-4ca2-b9a4-3ae2821e581c/ascreenshot.jpeg?tl_px=767,127&br_px=1541,560&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=568,191)


95\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/878e4ac2-b855-4e4b-89ff-907b20931fbe/ascreenshot.jpeg?tl_px=767,5&br_px=1541,438&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=688,191)


96\. Click here.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/2902c900-bd18-46bd-a159-668c84275b3d/ascreenshot.jpeg?tl_px=767,5&br_px=1541,438&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=688,191)


97\. Click "Add"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/27e1d84c-918a-483d-86ae-12acdde4032e/ascreenshot.jpeg?tl_px=767,535&br_px=1541,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=659,376)


98\. Colocamos una tarea a continuacion de la que agregamos.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/92205c3e-9719-49fd-adc0-d980167eccae/ascreenshot.jpeg?tl_px=231,535&br_px=1004,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=362,234)


99\. Click the "Search tasks" field.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/0fc56d1a-f0bd-4304-81a2-639b5df737ac/ascreenshot.jpeg?tl_px=767,0&br_px=1541,432&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=484,159)


100\. Type "sona"


101\. Click "Publish Quality Gate Result"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/01add8e9-6e46-45f0-afc2-9e9678562c57/ascreenshot.jpeg?tl_px=767,68&br_px=1541,501&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=534,191)


102\. Click "Add"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/1a568ee8-a1d8-41e7-88c4-72e3f69b8260/ascreenshot.jpeg?tl_px=767,535&br_px=1541,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=659,383)


103\. Corregimos indentación y Click "Validate and save"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/5162dc93-9a8e-4dc0-94cb-477fb84e5dd2/ascreenshot.jpeg?tl_px=0,0&br_px=1541,968&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=976,21)


104\. Click "Save"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/5147c69b-f5ed-492c-bdd1-80cad0fceff5/ascreenshot.jpeg?tl_px=767,535&br_px=1541,968&force_format=jpeg&q=100&width=774&wat_scale=69&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=677,362)

### 5.2 Inicializar Cypress en nuestro proyecto

1\. En la ventana del navegador que se abre hacer click en E2E Testing

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/d6c15ba2-a364-4ab1-b542-884aa0d86c6a/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1451,719&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=374,271)


2\. Nos indica que creo una serie de archivos necesarios para el funcionamiento, click en "Continue"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/6a8b4c00-14ae-45c1-acad-d05574735f78/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1438,786&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=223,453)


3\. Seleccionar un browser y darle "Start E2E Testing in &lt;BROWSER_Seleccionado&gt;"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/41784e8e-cce9-47d0-b7e4-41fd1bc8c91a/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1438,878&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=479,312)


4\. Hacemos que nos cree una serie de pruebas de ejemplo

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/666c45c9-66ea-409a-82f8-3f58a6eab2d6/ascreenshot.jpeg?tl_px=0,0&br_px=2880,1800&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=482,356)


5\. Click "Okay, I got it!"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/295462aa-efee-4c07-89f1-72353be0edb0/ascreenshot.jpeg?tl_px=0,0&br_px=2880,1800&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=337,530)


6\. Seleccionar una de las pruebas generadas

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/63b4bcff-5ade-48ba-b737-4425160eca7a/ascreenshot.jpeg?tl_px=0,469&br_px=1719,1430&force_format=jpeg&q=100&width=1120.0&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=427,276)


7\. Vemos que se corre una prueba automatizada.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/c65a7eec-eee0-4b62-a917-89d51b7bb007/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2880,1800&force_format=jpeg&q=100&width=1120.0)


8\. Click "Salir"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/723e3ef7-795f-4b68-82d6-f4d774c5b1bf/ascreenshot.jpeg?tl_px=1060,599&br_px=1920,1080&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=531,308)


9\. Click the Scribe - Desktop App: Uso de memoria: 71.5 MB tabulador

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/402c13b9-b8a8-4470-992e-1170fb5586e5/ascreenshot.jpeg?tl_px=702,0&br_px=1562,480&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,19)


10\. Click the Cerrar botón

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/28e8eb93-b226-473b-bdb5-f3fe5bfd4e89/ascreenshot.jpeg?tl_px=743,0&br_px=1603,480&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,22)


11\. Click the Scribe
Tiene acceso a este sitio botón emergente

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/67b50aae-bd06-4154-98b2-b73fdd42a9a2/ascreenshot.jpeg?tl_px=1060,0&br_px=1920,480&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=681,72)


12\. Click "Start Capture"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/b809dec5-15d3-4894-a972-e8c3609d7d0d/ascreenshot.jpeg?tl_px=1060,137&br_px=1920,618&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=690,212)


13\. Click "Launch Scribe Desktop"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/71c8600f-ab19-4db4-a8f8-37862505635b/ascreenshot.jpeg?tl_px=1060,164&br_px=1920,645&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=579,212)


14\. Click the Abrir Scribe Desktop.app botón

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-09-12/330f3c84-4615-44e9-975d-f45857cddecc/ascreenshot.jpeg?tl_px=640,31&br_px=1500,512&force_format=jpeg&q=100&width=860&wat_scale=76&wat=1&wat_opacity=0.7&wat_gravity=northwest&wat_url=https://colony-recorder.s3.us-west-1.amazonaws.com/images/watermarks/FB923C_standard.png&wat_pad=402,212)



### 6-  Presentación del trabajo práctico.
- Subir un doc al repo de GitHub con las capturas de pantalla de los pasos realizados. Debe ser un documento (md, word, o pdf), no videos. Y el documento debe seguir los pasos indicados en el Desarrollo del TP.
- Acceso al repo de Azure Devops para revisar el trabajo realizado.

### 7-  Criterio de Calificación
Los pasos 4.1 al 4.3 representan un 60% de la nota total, los pasos 4.4 y subsiguientes representan el 40% restante.




