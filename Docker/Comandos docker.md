

## Crear una imagen de una imagen de un contenedor a partir del docker file:

	docker build -t [imagen]:[tag] (ubicación)

 	docker build -t my_app:v2 .


Instalar un almacenamiento local para un contenedor:
------------------------------------------------------

docker run -v /ruta_local:/ruta_contenedor (resto de comandos para hacer run al contenedor)

	docker run -v ${pwd}/etc:/app/etc/todos -dp 3000:3000 my_app   // No vale poner .. , porque powersell no lo gestiona bien 

nota:
La ruta en ambos debe estar completa, por que si no no se mapean bién la cosas. Importante que la ruta local sobreescribe la ruta que hay en el contenedor, por lo que puede ser util tener una carpeta aparte en el contenedor para almacenar estas cosas

nota2:
El prefijo ${pwd} es necesario en sistemas windows, pues no identificará bien la ruta si no se especidfica donde estamos.

Almacenamientos temporales:
---------------------------
Existe un tipo de almacenamientos en los que la información no persiste nisiquiera entre paradas del contenedor, esto puede interesarnos para algunos contenedores que crean datos temporales que se utilizan durante la ejecución, pero que no se requieren de una sesión a la siguiente. Estos contenedores se llaman TMPFS:

	docker run --tmpfs /Almacenamiento_temporal (imagen)


Mapear una carpeta para poder cambiar archivos internos dentro de un contenedor:
--------------------------------------------------------------------------------

docker run -v /ruta_local:/ruta_contenedor -dp 3000:3000 -v /ruta_local2:/ruta_contenedor2 my_app

Ej:
	docker run -v ${pwd}/etc:/app/etc/todos -dp 3000:3000 -v ${pwd}/src:/app/src my_app
	
Mapear unvolumen de un contenedor a otro:
-----------------------------------------

Podemos hacer que un volumen nuevo comparta todos los volúmenes de un contenedor ya existente.

	docker run --volumes-from (contenedor) (imagen)

Limitar la memoria para que no se pueda crear una imagen de tamaño superior al establecido:
-------------------------------------------------------------------------------

	docker run -m 128m (nombre de la imagen)

Establece un contenedor con un tamaño inicial de 128mB, pero este tamaño podría aumentar en múltiplos de dos en caso de ser requerido por un comando dentro del contenedor

	docker run -m 128 --memory-swap 128m (nomre de la imagen)

Establece un contenedor con un tamaño inicial de 128mB, estando este tamaño limitado a 128mB con lo que el tamaño no podría escalar a un tamaño mayor.

Limitación de CPU:
-------------------

Podemos limitar la cantidad de CPU que dejamos tener a un contenedor:

	docker run -c (número) (nombre de la imagen)

	el valor por defecto de este -c es 1024

Para ver la cantidad de CPU que utiliza cada contenedor podemos usar la siguiente instrucción:

	docker stats $(docker inspect -f "{{.Name}}" $(docker ps -q))

Esto muestra las estadísticas de uso de CPU en porcentaje donde 100% es el uso de un núcleo completo, por ello los contenedores pueden tener asignado un valor superior al 100%

Otra forma es limitar el tiempo de CPU que tiene cada contenedor:

	docker tun -d --cpu-period=50000 --cpu-quota=25000 (imagen)

Esto hace que en un periodo de 50000 us este conteneor tendría acceso a la CPU durante 25000 us, esto suponiendo un sistema de una CPU

Limitación de y por reinicios:
------------------------------

Se puede limitar el numero de reinicios que puede tener un contenedor, así como cambiar como un contenedor reacciona al ser apagado haciendo que se reinicie:

	docker run -d --restart=on-failure:10 (imagen)

Esto hace que el contenedor se reinicie en caso de fallo con un límite de 10 veces y ademas multiplica por 2 el tiempo que tarda el contene  dor en volver a lanzarse después de haber sido tumbado por la razón que sea.

Podemos ver la cantidad de veces que se ha reiciniado los contenedores:

	docker inspect -f "{{.RestartCount}}" ($docker ps -sq)

Ejecutar un comando dentro dentro de un contenedor y obtener la salida:
-----------------------------------------------------------------------
Para ejecutar un comando interno del contenedor, ya sea para ejecutar una instrucción tipica de un entorno linux o un ejecutable dentro del contenedor podemos usar el comando exec

	docker exec (nombre del contenedor) (comando a ejecutar)
	
Por ejemplo para ejecutar un bash dentro del contenedor:

	docker exec -it ubuntu3(nombre) bash

Esto desplegaría la línea de comandos de manera similar a cuando hacemos

	docker run -it ubuntu3
	
Ejemplo:

	docker exec nginx1 ls /usr/
	bin
	games
	include
	lib
	lib64
	libexec
	
Obtener la salida que está generando un contenedor:
-----------------------------

Cuando tenenemos un contenedor ya corriendo y ejecutando algún proceso, podemos engancharnos a ese proceso mediante el comando attach. En el caso de que este proceso, dentro del contenedor, devuelva una salida lo que obtendremos será esa salida, por defecto en un contenedor ubuntu o algun SO que por defecto ejecute un bash nos devolverá la linea de comandos

	docker attach (nombre del contenedor)



Limitación en sistemas de archivos:
-----------------------------------

Podemos hacer que el sistema de archivos de un contenedor tenga solo permisos de lectura:

	docker run --read-only (imagen)

los archivos del contenedor no serán modificables. También se puede hacer que un volumen sea de solo lectura con la siguiente instrucción:

	docker run -v $(pwd):/pwd:ro (imagen)

los archivos dentro de /pwd tendrán permisos de solo lectura


Limitación de capacidades:
--------------------------

Se refiere a los permisos que tienen determinados procesos que corren dentro del contenedor. Por defecto un contenedor no puede acceder a capacidades del sistema como la GPU o la targeta de sonido. 

	docker run devian date -s (una fecha) 
	
Intentaría cambiar la fecha dentro del contenedor, pero esto no está permitido por defecto.

	docker run --cap-add SYS_TIME devian date (una fecha) 

Al haber dado los permisos necesarios al contenedor este sería capáz de hacer el cambio de fecha.

Otro enfoque es, en lugar de el de dar permisos, el de quitar todos los permisos existentes al contenedor y luego darle los que el contenedor realmente vaya a necesitar, con lo que se reducen mucho las capacidades a las que tiene acceso el sistema.

	docker run --cap-drop all (Imagen)

posteriormente agregaríamos los permisos necesarios para nuestra applicación.

Creación de usuario:
--------------------

Esto creo que sale en la clase algo así en la ultima clase del curso.

	run groupadd -r user 

Leer los logs de un contenedor:
--------------------------------------------------------------------------------
Muestra los logs que hay en un determinado contenedor

	docekr logs [contenedor]
	
Para mostrar solo las X ultimas lineas usamos --tail

	docker logs (nombre) --tail 10

Para obtener los logs de manera continua usamos -f, se queda esperando a la llegada de nuevos logs.

	docekr logs -f [contenedor]

obtener información del proceso principal:
-----------------------------------------

Para obtener ingormación los procesos mas demandante podemos hacer uso de top

	docker top (contenedor)


ver el estado de todos los contenedores:
--------------------------------------------------------------------------------
Aquí lo que se está haciendo es obtener con inspect el json (que determina el contenedor)
y a partir de este se obtienen los nombres de cada uno de estos contenedores, (se
obtienen los json de todos los contenedores activos al no usar -a ) y a continuación se
le pasan esos nombres al comando docker stats que obtiene metricas en tiempo real.

	docker stats $(docker inspect -f '{{.Name}}' $(docker ps -q))
