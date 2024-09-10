## Trabajo Práctico 5 - Despliegue de aplicaciones con Azure Devops Release Pipelines

### 4- Desarrollo:
4.1\. Crear una cuenta en Azure
![alt text](imagenes/0.png)

4.2\. Crear un recurso Web App en Azure Portal y navegar a la url provista
![alt text](imagenes/1.png)
![alt text](imagenes/2.png)
![alt text](imagenes/3.png)

4.3\. Actualizar Pipeline de Build para que use tareas de DotNetCoreCLI@2 como en el pipeline clásico, luego crear un Pipeline de Release en Azure DevOps con CD habilitada
![alt text](imagenes/4.png)
![alt text](imagenes/5.png)

4.4\. Optimizar Pipeline de Build 4.5\. Verificar el deploy en la url de la WebApp /weatherforecast
4.6\. Realizar un cambio al código del controlador para que devuelva 7 pronósticos, realizar commit, evaluar ejecución de pipelines de build y release, navegar a la url de la webapp/weatherforecast y corroborar cambio
![alt text](imagenes/6.png)
![alt text](imagenes/7.png)
![alt text](imagenes/8.png)
![alt text](imagenes/9.png)

4.7\. Clonar la Web App de QA para que contar con una WebApp de PROD a partir de un Template Deployment en Azure Portal y navegar a la url provista para la WebApp de PROD.
![alt text](imagenes/10.png)
![alt text](imagenes/11.png)

4.8\. Agregar una etapa de Deploy a Prod en Azure Release Pipelines 
![alt text](imagenes/12.png)
![alt text](imagenes/13.png)

4.9\.  Realizar un cambio al código del controlador para que devuelva 10 pronósticos, realizar commit, evaluar ejecución de pipelines de build y release, navegar a la url de la webapp/weatherforecast y corroborar cambio, verificar que en la url de la webapp_prod/weatherforecast se muestra lo mismo.

- Como vemos luego de hacer el cambio para mostrar 10 pronosticos vemos la ejecucion de ambos pipelines (build y release):
![alt text](imagenes/14.png)
![alt text](imagenes/15.png)

- Aca los vemos ya finalizados:
![alt text](imagenes/16.png)
![alt text](imagenes/17.png)

- Corroboramos los cambios en la url webapp/weatherforecast y en la url de la webapp_prod/weatherforecast.
![alt text](imagenes/18.png)
![alt text](imagenes/19.png)

4.10\. Modificar pipeline de release para colocar una aprobación manual para el paso a Producción.
![alt text](imagenes/20.png)

4.11\. Realizar un cambio al código del controlador para que devuelva 5 pronósticos, realizar commit, evaluar ejecución de pipelines de build y release, navegar a la url de la webapp/weatherforecast y corroborar cambio, verificar que en la url de la webapp_prod/weatherforecast aun se muestra la versión anterior.

4.12\. Aprobar el pase ya sea desde el release o desde el mail recibido. 

4.12.1\. Notar que se puede dar la aprobación pero posponer su aplicación hasta una determinada fecha

4.13\. Esperar a la finalización de la etapa de Pase a Prod y luego corroborar que en la url de la webapp_prod/weatherforecast se muestra la nueva versión coinicidente con la de QA.

- Hacemos el cambio para mostrar 5 pronosticos vemos la ejecucion de ambos pipelines (build y release):
![alt text](imagenes/21.png)
![alt text](imagenes/22.png)

- Aca los vemos ya finalizados, pero en el releases vemos ahora que a diferencia del otro este tiene una aprobacion manual para el paso a produccion:
![alt text](imagenes/23.png)
![alt text](imagenes/24.png)
![alt text](imagenes/25.png)

- Corroboramos los cambios en la url webapp/weatherforecast y en la url de la webapp_prod/weatherforecast luego de hacer la aprobacion.
![alt text](imagenes/26.png)
![alt text](imagenes/27.png)


4.14\. Realizar un pipeline (no release) que incluya el deploy a QA y a PROD con una aprobación manual. El pipeline debe estar construido en YAML sin utilizar el editor clásico de pipelines ni el editor clásico de pipelines de release.
![alt text](imagenes/28.png)
![alt text](imagenes/30.png)
![alt text](imagenes/29.png)
![alt text](imagenes/31.png)
![alt text](imagenes/32.png)
![alt text](imagenes/33.png)
![alt text](imagenes/34.png)
![alt text](imagenes/35.png)


