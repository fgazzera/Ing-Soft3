## Trabajo Práctico 1 - Git Básico

#### 1- Instalar Git
En mi caso ya lo tenia instalado con la versión 2.44.0
![alt text](imagenes/1.png)

#### 2- Crear un repositorio local y agregar archivos
  - Crear un repositorio local en un nuevo directorio.
  ![alt text](imagenes/2.png)
  - Agregar un archivo Readme.md, agregar algunas líneas con texto a dicho archivo.
  ![alt text](imagenes/3.png)
  ![alt text](imagenes/4.png)
  - Crear un commit y proveer un mensaje descriptivo.
  ![alt text](imagenes/5.png)

#### 3- Configuración del Editor Predeterminado
 - Instalar Notepad ++ para Windows o TextMate para Mac OS, colocarle un alias y configurarlo como editor predeterminado.
 
    En mi caso instale Notepad++, pero voy a definir a Visual Code como editor predeterminado ya que es el que uso.
   ![alt text](imagenes/6.png)
- Para Visual Code:
    ```sh
    git config --global core.editor "code --wait"
    ```
   
#### 4- Creación de Repos 01 -> Crearlo en GitHub, clonarlo localmente y subir cambios
  - Crear una cuenta en https://github.com
  - Crear un nuevo repositorio en dicha página con el Readme.md por defecto
    ![alt text](imagenes/7.png)
  - Clonar el repo remoto en un nuevo directorio local
    Debemos copiar el URL del nuevo repositorio para clonarlo en el directorio local
    ![alt text](imagenes/8.png)
    Para clonarlo al directorio local usamos:
    ```sh
    git clone https://github.com/fgazzera/Repo1_tp1.git
    ```
    ![alt text](imagenes/9.png)
  - Editar archivo Readme.md agregando algunas lineas de texto
    ![alt text](imagenes/10.png)
  - Editar (o crear si no existe) el archivo .gitignore agregando los archivos *.bak
    ![alt text](imagenes/11.png)
    ![alt text](imagenes/12.png)
  - Crear un commit con un mensaje descriptivo. Luego Intentar hacer un push al repo remoto
    ![alt text](imagenes/13.png)

#### 5- Creación de Repos 02-> Crearlo localmente y subirlo a GitHub
  - Crear un repo local
  - Agregar archivo Readme.md con algunas lineas de texto
  - Crear repo remoto en GitHub
  - Asociar repo local con remoto
  - Crear archivo .gitignore
  - Crear un commit y proveer un mensaje descriptivo
  - Subir cambios.

#### 6- Ramas
  - Crear una nueva rama
  - Cambiarse a esa rama
  - Hacer un cambio en el archivo Readme.md y hacer commit
  - Revisar la diferencia entre ramas

#### 7- Merges
  - Hacer un merge FF
  - Borrar la rama creada
  - Ver el log de commits
  - Repetir el ejercicio 6 para poder hacer un merge con No-FF

#### 8- Resolución de Conflictos
  - Instalar alguna herramienta de comparación. Idealmente una 3-Way:
    - P4Merge https://www.perforce.com/downloads/helix-visual-client-p4v:
![alt text](p4merge.png)
    - Se puede omitir registración. Instalar solo opción Merge and DiffTool.
 - ByondCompare trial version https://www.scootersoftware.com/download.php
    - Configurar Tortoise/SourceTree para soportar esta herramienta.
    - https://www.scootersoftware.com/support.php?zz=kb_vcs
    - https://medium.com/@robinvanderknaap/using-p4merge-with-tortoisegit-87c1714eb5e2
  - Crear una nueva rama conflictBranch
  - Realizar una modificación en la linea 1 del Readme.md desde main y commitear
  - En la conflictBranch modificar la misma línea del Readme.md y commitear
  - Ver las diferencias con git difftool main conflictBranch
  - Cambiarse a la rama main e intentar mergear con la rama conflictBranch
  - Resolver el conflicto con git mergetool
  - Agregar .orig al .gitignore
  - Hacer commit y push

#### 9- Familiarizarse con el concepto de Pull Request

  - Explicar que es un pull request.
  - Crear un branch local y agregar cambios a dicho branch. 
  - Subir el cambio a dicho branch y crear un pull request.
  - Completar el proceso de revisión en github y mergear el PR al branch master.


#### 10- Algunos ejercicios online
  - Entrar a la página https://learngitbranching.js.org/
  - Completar los ejercicios **Introduction Sequence**
  - Opcional - Completar el resto de los ejercicios para ser un experto en Git!!!

#### 11- Crear Repositorio de la materia
  - Crear un repositorio para la materia en github. Por ejemplo **ing-software-3**
  - Subir archivo(s) .md con los resultados e imágenes de este trabajo práctico. Puede ser en una subcarpeta **trabajo-practico-01**

### Referencias

- https://try.github.io/
- https://github.github.com/training-kit/downloads/es_ES/github-git-cheat-sheet.pdf
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

