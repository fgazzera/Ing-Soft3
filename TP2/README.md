## Trabajo Práctico 2 - Introducción a Docker

### 1- Instalar Docker Community Edition 
- En mi caso ya tenia instalado Docker con la siguiente version:   
![captura](imagenes/1.png)

### 2- Explorar DockerHub
- Registrase en docker hub: https://hub.docker.com/
![captura](imagenes/2.png)

### 3- Obtener la imagen BusyBox
- Ejecutar el siguiente comando, para bajar una imagen de DockerHub
```bash
docker pull busybox
```
![captura](imagenes/3.png)

- Verificar qué versión y tamaño tiene la imagen bajada, obtener una lista de imágenes locales:
```bash
docker images
```
![captura](imagenes/4.png)


### 4- Ejecutando contenedores
- Ejecutar un contenedor utilizando el comando **run** de docker:
```bash
docker run busybox
```
![captura](imagenes/5.png)

- Explicar porque no se obtuvo ningún resultado
  - Si ejecutamos el comando "docker run busybox" sin especificar ningún comando adicional, Docker intentará iniciar un contenedor usando la imagen de BusyBox y ejecutará el comando por defecto que está configurado en esa imagen, pero como no se ha especificado que se ejecute de manera interactiva, el contenedor se iniciará y se detendrá de inmediato.

- Especificamos algún comando a correr dentro del contenedor, ejecutar por ejemplo:
```bash
docker run busybox echo "Hola Mundo"
```
![captura](imagenes/6.png)

- Ver los contenedores ejecutados utilizando el comando **ps**:
```bash
docker ps
```
![captura](imagenes/7.png)

- Vemos que no existe nada en ejecución, correr entonces:
```bash
docker ps -a
```
![captura](imagenes/8.png)

- Mostrar el resultado y explicar que se obtuvo como salida del comando anterior.
  - El segundo comando muestra contenedores que han terminado. Vemos al contenedor BusyBox que terminó después de ejecutar el comando echo.

### 5- Ejecutando en modo interactivo

- Ejecutar el siguiente comando
```bash
docker run -it busybox sh
```
![captura](imagenes/9.png)

- Para cada uno de los siguientes comandos dentro de contenedor, mostrar los resultados:
```bash
ps
uptime
free
ls -l /
```
![captura](imagenes/10.png)
![captura](imagenes/11.png)

- Salimos del contenedor con:
```bash
exit
```
Vemos que al hacer exit el contenedor se detiene:
![captura](imagenes/12.png)


### 6- Borrando contenedores terminados

- Obtener la lista de contenedores 
```bash
docker ps -a
```
![captura](imagenes/13.png)

- Para borrar podemos utilizar el id o el nombre (autogenerado si no se especifica) de contenedor que se desee, por ejemplo:
```bash
docker rm elated_lalande
```
- Para borrar todos los contenedores que no estén corriendo, ejecutar cualquiera de los siguientes comandos:
```bash
docker rm $(docker ps -a -q -f status=exited)
```
```bash
docker container prune
```
- En mi caso utilice la ultima opcion:
![captura](imagenes/14.png)


### 7- Construir una imagen
- Conceptos de DockerFile
  - Leer https://docs.docker.com/engine/reference/builder/ 
  - Describir las instrucciones
     - FROM: Define la imagen base en la etapa de construccion.
     - RUN: Ejecuta comandos de compilacion durante la construcción de la imagen.
     - ADD: Copia archivos/directorios al contenedor.
     - COPY: Copia archivos y directorios.
     - EXPOSE: Describe en qué puertos escucha(utiliza) tu aplicación.
     - CMD: Define el comando predeterminado para ejecutar al iniciar el contenedor.
     - ENTRYPOINT: Define el comando que se ejecutará siempre como predeterminado.
- A partir del código https://github.com/ingsoft3ucc/SimpleWebAPI crearemos una imagen.
- Clonar repo
- Crear imagen etiquetándola con un nombre. El punto final le indica a Docker que use el dir actual
```
docker build -t    .
```
![captura](imagenes/15.png)
![captura](imagenes/16.png)

- Revisar Dockerfile y explicar cada línea
![captura](imagenes/17.png)

- Ver imágenes disponibles
- Ejecutar un contenedor con nuestra imagen
![captura](imagenes/18.png)

- Subir imagen a nuestra cuenta de dockerhub
  - 7.1 Inicia sesión en Docker Hub
    - Primero, asegúrate de estar autenticado en Docker Hub desde tu terminal:
    ```bash
    docker login
    ```
![captura](imagenes/19.png)

  - 7.2 Etiquetar la imagen a subir con tu nombre de usuario de Docker Hub y el nombre de la imagen. Por ejemplo:
    ```bash
    docker tag <nombre_imagen_local> <tu_usuario_dockerhub>/<nombre_imagen>:<tag>
    ```
![captura](imagenes/20.png)

  - 7.3 Subir la Imagen
    - Para subir la imagen etiquetada a Docker Hub, utiliza el comando docker push:
     ```bash
     docker push <tu_usuario_dockerhub>/<nombre_imagen>:<tag>
     ```
![captura](imagenes/21.png)

  - 7.4 Verificar la Subida
     ```bash
     docker pull <tu_usuario_dockerhub>/<nombre_imagen>:<tag>
     ```
![captura](imagenes/22.png)


### 8- Publicando puertos

En el caso de aplicaciones web o base de datos donde se interactúa con estas aplicaciones a través de un puerto al cual hay que acceder, estos puertos están visibles solo dentro del contenedor. Si queremos acceder desde el exterior deberemos exponerlos.

  - Ejecutar la siguiente imagen, en este caso utilizamos la bandera -d (detach) para que nos devuelva el control de la consola:

```bash
docker run --name myapi -d mywebapi
```
  - Ejecutamos un comando ps:
  - Vemos que el contendor expone 3 puertos el 80, el 5254 y el 443, pero si intentamos en un navegador acceder a http://localhost/WeatherForecast no sucede nada.

  - Procedemos entonces a parar y remover este contenedor:
```bash
docker kill myapi
docker rm myapi
```
  - Vamos a volver a correrlo otra vez, pero publicando el puerto 80

```bash
docker run --name myapi -d -p 80:80 mywebapi
```
![captura](imagenes/23.png)

  - Accedamos nuevamente a http://localhost/WeatherForecast y vemos que nos devuelve datos.
![captura](imagenes/24.png)

### 9- Modificar Dockerfile para soportar bash 

- Modificamos dockerfile para que entre en bash sin ejecutar automaticamente la app
![captura](imagenes/25.png)
 
```bash
#ENTRYPOINT ["dotnet", "SimpleWebAPI.dll"]
CMD ["/bin/bash"]
```
- Rehacemos la imagen
![captura](imagenes/26.png)

```
docker build -t mywebapi .
```
- Corremos contenedor en modo interactivo exponiendo puerto
![captura](imagenes/27.png)
```
docker run -it --rm -p 80:80 mywebapi
```
- Navegamos a http://localhost/weatherforecast
- Vemos que no se ejecuta automaticamente
![captura](imagenes/28.png)
- Ejecutamos app:
![captura](imagenes/29.png)
```
dotnet SimpleWebAPI.dll
```
- Volvemos a navegar a http://localhost/weatherforecast
![captura](imagenes/30.png)
- Salimos del contenedor


  
### 10- Montando volúmenes

Hasta este punto los contenedores ejecutados no tenían contacto con el exterior, ellos corrían en su propio entorno hasta que terminaran su ejecución. Ahora veremos cómo montar un volumen dentro del contenedor para visualizar por ejemplo archivos del sistema huésped:

  - Ejecutar el siguiente comando, cambiar myusuario por el usuario que corresponda. En Mac puede utilizarse /Users/miusuario/temp):
![captura](imagenes/31.png)
```bash
docker run -it --rm -p 80:80 -v /Users/miuser/temp:/var/temp  mywebapi
```
  - Dentro del contenedor correr
```bash
ls -l /var/temp
touch /var/temp/hola.txt
```
  - Verificar que el Archivo se ha creado en el directorio del guest y del host.
![captura](imagenes/32.png)


### 11- Utilizando una base de datos
- Levantar una base de datos PostgreSQL
![captura](imagenes/33.png)
![captura](imagenes/34.png)

```bash
mkdir $HOME/.postgres

docker run --name my-postgres -e POSTGRES_PASSWORD=mysecretpassword -v $HOME/.postgres:/var/lib/postgresql/data -p 5432:5432 -d postgres:9.4
```
- Ejecutar sentencias utilizando esta instancia
![captura](imagenes/35.png)

```bash
docker exec -it my-postgres /bin/bash

psql -h localhost -U postgres

#Estos comandos se corren una vez conectados a la base
![captura](imagenes/36.png)

\l
create database test;
\connect test
create table tabla_a (mensaje varchar(50));
insert into tabla_a (mensaje) values('Hola mundo!');
select * from tabla_a;

\q

exit
```

- Conectarse a la base utilizando alguna IDE (Dbeaver - https://dbeaver.io/, Azure DataStudio -https://azure.microsoft.com/es-es/products/data-studio, etc). Interactuar con los objectos objectos creados.
![captura](imagenes/37.png)

- Explicar que se logro con el comando `docker run` y `docker exec` ejecutados en este ejercicio.
- El comando docker run es utilizado para crear e iniciar un nuevo contenedor a partir de una imagen, en este caso de PostgreSQL. En este tambien configuramos la contraseña para el usuario principal de PostgreSQL para luego poder acceder a la base de datos.
- El comando docker exec se utiliza para ejecutar comandos dentro de un contenedor en ejecución. En mi caso lo utilice para acceder al contenedor my-postgres que ya estaba corriendo.

### 12- Hacer el punto 11 con Microsoft SQL Server
- Armar un contenedor con SQL Server
- Crear BD, Tablas y ejecutar SELECT
- Paso 1: Descargamos y levantamos un contenedor en docker de SQL Server 2019
```bash
docker pull mcr.microsoft.com/mssql/server:2019-latest

docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=YourPassword123' -p 1433:1433 --name sql_server_container -d mcr.microsoft.com/mssql/server:2019-latest
```
![captura](imagenes/38.png)

- Paso 2: Ahora debemos acceder a la BD desde SQL Server o Azure. En mi caso voy a ingresar desde Azure.
![captura](imagenes/39.png)

- Paso 3: Vamos a ejecutar las siguientes sentencias de SQL para crear una BD, Tablas y usar el SELECT:
![captura](imagenes/40.png)
![captura](imagenes/41.png)
![captura](imagenes/42.png)
