# Documentación de Cmake
## Resumen

El objetivo principal de CMake es la creación de el proyecto de compilación. Estos proyectos se pueden generar para distintos SO y distintos compiladores.
Generará los archivos de construcción como 

- makefile
- archivos de ninja
- vcxproj para compilar en windows

Finalmente se lanza la herramienta apropiada para la compilar el codigo.

Permite:

- Compilación multiplataforma
- Simplificación del proceso de compilación
- Optimización de la compilación (dividiendo el proceso)
- Automatización de la costrucción del proyecto
- Permite gestionar dependencias

## Inicio

Lo primero que necesitamos dentro de nuestro proyecto es una carpeta en la que colocar los archivos de compilación. Para ello creamos la carpeta:

    mkdir build

Luego para preparar la carpeta para almacenar los archivos de compilación debemos movernos a esa carpeta y ejecutar CMake .. en este caso el archivo CMakeList.txt debe estar en la carpeta superior

    cd .\build\
    cmake ..

Una vez tenemos el archivo CMakeList.txt preparado para poder compilar podemos ejecutar

    cmake --build .\build\ (windows)

    cmake --build build/  (linux)

En caso de querer generar el proyecto de nuevo desde cero se hace con la siguiente insttrucción:

    cmake -B build --fresh


## Comandos del archivo CMakeList.txt

Dentro de este archivo, que solo puede haber uno en cada directorio con ese nombre se colocan las instrucciones que indican las dependencias y otros parametros que se usarán para generar el proyecto de compilación.

### Versión minima requerída de cmake:
```Cmake
cmake_minimum_required(VERSION 3.15)
```

### Inicialización del proyecto:
```Cmake
project(imagelite
    VERSION 1.0.0
    DESCRIPTION "A lightweight image processing library and CLI tool"
    )
```

### Añadir un ejecutable:
vamos a crear un ejecutable, que en este caso coincidirá con el nombre del proyecto.

```Cmake
add_executable(NombreProyecto    apps/cli/src/main.cpp)
```

### Añadir una librería:
Añadimos las librerías core y image a un tartget, que es un 'paso' en la creación del proyecto. En este caso el target es NombreProyecto_core

```Cmake
add_library(NombreProyecto_core STATIC
    src/core.cpp
    src/image.cpp
)
```

### Inclusión de directorios en el target:
Se incluyen las referencias a los .h que necesita este target en concreto. Serán las dependencias que necesite NombreProyecto_core.

```Cmake
target_include_directories(NombreProyecto_core
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE ${PROJECT_SOURCE_DIR}/external/stb
)
```
### inclusión de los target que contienen: dependencias
```Cmake
TARGet_link_libraries(imagelite 
        PRIVATE 
            imagelite_core imagelite_filters)
```

### Inclusión de directorios necesarios:
Esta instrucción será necesaria antes de linckat los target que se encontraran en los directorios añadidos:
```Cmake
add_subdirectory(libs/core)
add_subdirectory(libs/filters)
```

### Creación de targets especificos para una compilación
En el momento de la compilación podemos hacer que cmake ejecute el programa por nosotros:

```Cmake
add_custom_target(gray
    COMMAND NombreProyecto grayscale ${SAMPLE_IMAGES_DIR}/input.jpg ${SAMPLE_IMAGES_DIR}/output-gray.jpg 
    # (otro ejemplo) COMMAND imagelite blur ${SAMPLE_IMAGES_DIR}/input.jpg ${SAMPLE_IMAGES_DIR}/output-blur.jpg 5
    DEPENDS NombreProyecto
    WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
    COMMENT "---- Running imagelite (nombre del proyecto) grayscale command ----"
)
```

- COMMAND: es el comando que se ejecutará, se escribe el nombre del proyecto y después los parametros de entrada.
- DEPENDS: se asegura de generar las dependencias antes de ejecutar el comando.
- WORKING_DIRECTORY: le dice donde encontrar el ejecutable
- COMMENT: Un comentario que se generará al ejecutar el comando


⚠️ He probado a ejecutarlo de la siguiente forma y funciona igualmente, no lo entiendo⁉️⁉️⁉️
```Cmake
add_custom_target(gray
    COMMAND imagelite grayscale ${SAMPLE_IMAGES_DIR}/input.jpg ${SAMPLE_IMAGES_DIR}/output-gray.jpg 
    #DEPENDS imagelite
    #WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
    #COMMENT "---- Running imagelite grayscale command ----"
)
```

### Creación de funciones CMake:

Para reducir la cantidad de código, como en otros lenguajes podemos hacer funciones:

Dentro de la función la variable `${ARGN}` representa los argumentos que se la pasan a la propia función, que no son las variables sino los archivos sobre los que se aplicará la acción dentro de la funcion.

El tipo de las variables `int, bool, texto` se determina dinamicamente según se les llama.

```Cmake
function(nombre_funcion variable1 variable2)
    # También se pueden usar condicionales dentro de la función
    if (${variable2})
        # Se pueden usar internamente comandos propios de cmake
        target_include_directories(${variable1}
            PUBLIC ${PROJECT_SOURCE_DIR}/libs
        )    
    else()
        # Se pueden usar internamente comandos propios de cmake
        target_include_directories(${variable1}
            PRIVATE ${PROJECT_SOURCE_DIR}/libs
        )    
    endif()
endfunction()
```

Ejemplo:

```Cmake
function(add_imagelite_module name use_stb)
    add_library(${name} STATIC ${ARGN})

    target_include_directories(${name}
        PUBLIC 
            ${CMAKE_CURRENT_SOURCE_DIR}/include
    )

    if (${use_stb})
        target_include_directories(${name}
            PRIVATE ${PROJECT_SOURCE_DIR}/external/stb
        )
    endif()
endfunction()
```

### Generación automática de un archivo

Primero generamos una receta con la que se se creará un archivo de salida
```Cmake
add_custom_command( # Este commando determina como se crea el archivo build_info.txt
    OUTPUT ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "imagelite Build Information:"
     > ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "CMake Version: ${CMAKE_VERSION}"
     >> ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "C++ Compiler: ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}"
     >> ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "System: ${CMAKE_SYSTEM_NAME} ${CMAKE_SYSTEM_VERSION}"
     >> ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "Proyect Version: ${PROJECT_VERSION} written in ${PROJECT_LANGUAGES}"
     >> ${CMAKE_BINARY_DIR}/build_info.txt
    COMMAND ${CMAKE_COMMAND} -E echo "Proyect Description: ${PROJECT_DESCRIPTION}"
     >> ${CMAKE_BINARY_DIR}/build_info.txt
    COMMENT "Generating detailed build information file"
    VERBATIM  #Se asegura de que todos los argumentos se pasan exactamente como han dido escritos, preservando espacios y caracteres especiales
)
```
Debemos tener también un custom target para "disparar" la generación del archivo al hacer que este dependa de el archivo para su ejecución. El ALL hace que este target se cree siempre en el momento de la compilación, con idependencia de que modulos sean requeridos.

```Cmake
add_custom_target(build_info ALL # This target it will be executed every time you build the project
    DEPENDS ${CMAKE_BINARY_DIR}/build_info.txt
    COMMENT "Build information file generated at ${CMAKE_BINARY_DIR}/build_info.txt"
)
```

Un custom_command también puede estar ligado a la ejecución de un target concreto, se puede especificar si el command se lanzará antes de la compilación de el target o después:

```Cmake
# Pre-build command
add_custom_command(TARGET imagelite
    PRE_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_SOURCE_DIR}/README.md" "${CMAKE_BINARY_DIR}/README.md"
    COMMAND ${CMAKE_COMMAND} -E echo "README.md copied to build directory."
    COMMAND ${CMAKE_COMMAND} -E make_directory "${CMAKE_SOURCE_DIR}/temp"
    COMMAND ${CMAKE_COMMAND} -E echo "Temporary directory created in ${CMAKE_SOURCE_DIR}/temp"
    COMMENT "--- Pre-build steps ---"
)

# Post-build command
add_custom_command(TARGET imagelite
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E echo "Executable located at: ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/imagelite"
    COMMAND ${CMAKE_COMMAND} -E rm -rf "${CMAKE_SOURCE_DIR}/temp"
    COMMAND ${CMAKE_COMMAND} -E echo "Temporary directory in ${CMAKE_SOURCE_DIR}/temp deleted."
    COMMENT "--- Post-build steps ---"
)
```

### Creación de variables
```Cmake
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
```
En este caso CMAKE_RUNTIME_OUTPUT_DIRECTORY es una variable especial de Cmake en la que se le puede decir el directorio en el que se va a colocar el ejecutable resultado de la compilación. Pero la función para crear una variable es la misma.

### Variables interesantes

#### Directorio actual del archivo cmake en ejecución:
```Cmake
${CMAKE_CURRENT_SOURCE_DIR}
```
#### Directorio del archivo cmake principal:
```Cmake
${PROJECT_SOURCE_DIR}
```

#### Directorio donde se está haciendo la compilación:
```Cmake
${CMAKE_BINARY_DIR}
```

### Herramientas interesantes de cmake

A la hora de ejecutar un custom_command y lanzar dentro de el un commando con COMMAND podrmos lanzar un comando cualquiera. Pero cmake ofrece algunos comandos propios que si los ejecutamos usando la siguiente estructura nos permite abstraernos del SO en el que se ejecuten estas instrucciones.

```Cmake
COMMAND ${CMAKE_COMMAND} -E [comando]
```
Algunos de los comandos permitidos con -E son:

```Cmake
- COMMAND ${CMAKE_COMMAND} -E echo [msg] → imprime un mensaje.

- COMMAND ${CMAKE_COMMAND} -E touch [file] → crea un archivo vacío o actualiza su fecha de modificación.

- COMMAND ${CMAKE_COMMAND} -E copy [src] [dst] → copia un archivo.

- COMMAND ${CMAKE_COMMAND} -E copy_directory [src] [dst] → copia un directorio.

- COMMAND ${CMAKE_COMMAND} -E make_directory [directorio] → crea un directorio.

- COMMAND ${CMAKE_COMMAND} -E remove [file] → elimina un archivo.

- COMMAND ${CMAKE_COMMAND} -E env VAR=valor comando → ejecuta un comando con variables de entorno modificadas.
```