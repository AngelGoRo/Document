Comando de docker file:
-------------------------
ADD: Copia los archivos en 

CMD: Ejecuta un script en el inicio del contenedor. Esto hace que una instrucción Docker Run posterior anula esta instrucción.

	La diferencia con EntryPoint es que podemos darle en este caso argumentos de entrada, cosa que con CMD no se puede hacer.

COPY: Se usa para copiar archivos del contexto de contrucción en la propia imagen.

ENTRYPOINT: Establece un ejecutable y los argumentos por defecto, que se ejecutará al iniciarse el contendor. Los parametros
	que sean necesarios para la ejecución de una imagen es pasarán posteriormente al nombre del ejecutable. Se suele usar
	para inicialización antes de interpretar cualquier otra cosa.

ENV: Establece variables de entorno dentro de la imagen.  Se puede hacer referencia a ellas en posteriores instrucciónes del
	dockerfile.

FROM: Establece la imagen base para el dockerfile. Las instruccines posteriores se contrullen sobre esta imagen. Se recomienda 
	estblecer una eticketa al obtener una imagen por que si no la imagen se actualizará.

MMINTAINER: Establece metadatos de autor

ONBUILD: Es una instrucción que se usa cuando la imagen en cuestión se utiliza como capa base de otra imagen. Para procesar datos y 
	añadirlos a una imagen fija.

RUN: Ejecuta la instrucción dada en el contenedor y consigna el resultado.

USER: Establece el usuario que se usará en las instrucciones posteriores.

VOLUME: Para crear un volumen al que tendrá acceso la imagen. En caso de que en el host ya esté creado este volumen se copia su
	contenido al contenedo. Puede haber multiples declaraciones de volumen.

WORKDIR: Establece el directorio de trabajo para cualquier instrucción posterior. Determinando donde se van a llevar a cabo los procesos.
	En instrucciones posteriores se pueden usar rutas relativas y estas estarán en función del WORKDIR establecido anteriormente.