## Trabajo Práctico 1 - Git Básico

### 1- Instalar Git
En mi caso ya lo tenia instalado con la versión 2.44.0
![captura](imagenes/1.png)

### 2- Crear un repositorio local y agregar archivos
  1. Crear un repositorio local en un nuevo directorio
  ![captura](imagenes/2.png)
  2. Agregar un archivo Readme.md, agregar algunas líneas con texto a dicho archivo
  ![captura](imagenes/3.png)
  ![captura](imagenes/4.png)
  3. Crear un commit y proveer un mensaje descriptivo
  ![captura](imagenes/5.png)

### 3- Configuración del Editor Predeterminado
 1. Instalar Notepad ++ para Windows o TextMate para Mac OS, colocarle un alias y configurarlo como editor predeterminado.

    En mi caso instale Notepad++, pero voy a definir a Visual Code como editor predeterminado ya que es el que uso.
   ![captura](imagenes/6.png)
- Para Visual Code:
    ```sh
    git config --global core.editor "code --wait"
    ```
   
### 4- Creación de Repos 01 -> Crearlo en GitHub, clonarlo localmente y subir cambios
  1. Crear una cuenta en https://github.com
  2. Crear un nuevo repositorio en dicha página con el Readme.md por defecto
    ![captura](imagenes/7.png)
  3. Clonar el repo remoto en un nuevo directorio local
    Debemos copiar el URL del nuevo repositorio para clonarlo en el directorio local
    ![captura](imagenes/8.png)
    Para clonarlo al directorio local usamos:
    ```sh
    git clone https://github.com/fgazzera/Repo1_tp1.git
    ```
    ![captura](imagenes/9.png)
  4. Editar archivo Readme.md agregando algunas lineas de texto
    ![captura](imagenes/10.png)
  5. Editar (o crear si no existe) el archivo .gitignore agregando los archivos *.bak
    ![captura](imagenes/11.png)
    ![captura](imagenes/12.png)
  6. Crear un commit con un mensaje descriptivo. Luego Intentar hacer un push al repo remoto
    ![captura](imagenes/13.png)

### 5- Creación de Repos 02-> Crearlo localmente y subirlo a GitHub
  1. Crear un repo local
    ![captura](imagenes/14.png)
  2. Agregar archivo Readme.md con algunas lineas de texto
    ![captura](imagenes/15.png)
  3. Crear repo remoto en GitHub
    ![captura](imagenes/16.png)
  4. Asociar repo local con remoto
    ![captura](imagenes/17.png)
  5. Crear archivo .gitignore
    ![captura](imagenes/18.png)
  6. Crear un commit y proveer un mensaje descriptivo y subir cambios
    ![captura](imagenes/19.png)

### 6- Ramas
  1. Crear una nueva rama
    ![captura](imagenes/20.png)
  2. Cambiarse a esa rama
    ![captura](imagenes/21.png)
  3. Hacer un cambio en el archivo Readme.md y hacer commit
    ![captura](imagenes/22.png)
  4. Revisar la diferencia entre ramas
    ![captura](imagenes/23.png)

### 7- Merges
  1. Hacer un merge FF.
    ![captura](imagenes/24.png)
  2. Borrar la rama creada.
  En este caso borramos la rama y verificamos que solo exita la rama "main":
    ![captura](imagenes/25.png)
  3. Ver el log de commits.
    ![captura](imagenes/26.png)
  4. Repetir el ejercicio 6 para poder hacer un merge con No-FF.
    ![captura](imagenes/27.png)
    ![captura](imagenes/28.png)
    Anteriormente volvimos a la rama principal y luego hicimos el merge con No-FF
    ```sh
    git merge --no-ff newFeature2
    ```
    ![captura](imagenes/29.png)
  

### 8- Resolución de Conflictos
  1. Instalar alguna herramienta de comparación.
    En mi caso, uso VSCode y voy a utilizar la herramienta de comparación que ya trae incluida y que ya la tengo como predeterminada.
  2. Crear una nueva rama conflictBranch
    ![captura](imagenes/30.png)
  3. Realizar una modificación en la linea 1 del Readme.md desde main y commitear
  4. En la conflictBranch modificar la misma línea del Readme.md y commitear
  5. Ver las diferencias con git difftool main conflictBranch
  6. Cambiarse a la rama main e intentar mergear con la rama conflictBranch
  7. Resolver el conflicto con git mergetool
  8. Agregar .orig al .gitignore
  9. Hacer commit y push

### 9- Familiarizarse con el concepto de Pull Request

  - Explicar que es un pull request.
  - Crear un branch local y agregar cambios a dicho branch. 
  - Subir el cambio a dicho branch y crear un pull request.
  - Completar el proceso de revisión en github y mergear el PR al branch master.


### 10- Algunos ejercicios online
  - Entrar a la página https://learngitbranching.js.org/
  - Completar los ejercicios **Introduction Sequence**
  - Opcional - Completar el resto de los ejercicios para ser un experto en Git!!!

### 11- Crear Repositorio de la materia
  - Crear un repositorio para la materia en github. Por ejemplo **ing-software-3**
  - Subir archivo(s) .md con los resultados e imágenes de este trabajo práctico. Puede ser en una subcarpeta **trabajo-practico-01**

### Referencias

- https://try.github.io/
- https://github.github.com/training-kit/downloads/es_ES/github-git-cheat-sheet.pdf
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

