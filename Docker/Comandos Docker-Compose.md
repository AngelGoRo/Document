Comando de docker-compose:
-------------------------

El arvhivo docker-compose se estructura mediante tabulaciones que indican lo que está 
contenido dentro de el elemento superior inmediatamente menos tabulado. Se compone de 
la etiqueta services, que será la que englova todos los contenedores creados por el 
archivo.

services:

	app: 					#En este caso app sería el primer contenedor que se crearía en el archivo
	    image: my_app:v2	#Image indica la imagen a partir de la cual se crea el contenedor	
	    ports:				#Indica el puerto de entrada y el puerto de salida del contenedor
	      - 3000:3000
	    environment:		#Son las variables de entorno que tiene dentro el contenedor
	      MYSQL_HOST: mysql
	      MYSQL_USER: root
	      MYSQL_PASSWORD: my-secret-pw
	      MYSQL_DB: todos
	volumes: 				#Indica la ruta local en la que se crea el volumen y la ruta
							  en el contenedor, las cuales serán compartidas
    - C:\Proyectos\Curso Docker\multi-container\todo-mysql-data:/var/lib/mysql
    build: .				#Indica el punto donde se contrulle la aplicación
    networks:
     - prometheus			#establecemos una red que va a utilizar el contenedor

networks:					#Redes, están fuera de los servicios
  prometheus:				#Esta es la red que se va a desplegar
    driver: bridge			#Tipo de red que se va a levantar
    name: prometheus		#Nombre que va a recibir la red
    

Herramientas para usar con Docker:
-----------------------------------

Visualización de Logs: ELK
	ElasticSearch: Herramienta de busqueda en logs o para datos
	Logstash: Herramienta que reune los logs, los indexa y los normaliza
	Kibana: Herramienta de monitorización (interfaz gráfia) que aprovecha los datos de ElasticSearch

Visualización de metricas:
	cAdvisor: Interfaz web para la inspeccion del consumo y otras metricas de los contenedores de 
		Docker.

Sistema de monitorización y alarmas de con marca temporal:
	Prometeus: Modelo de datos multidimensional con datos de series temporales y pares clave valor, 
	 con un lenguaje de consulta muy flexible. La recopilacion de estas series se hace mediante http
	 usa una pasarela intermedia para el envío. 

	Grafana: Sistema para Visualización de metricas. Analización de metricas, registros y trazas.

	nueva contraseña: usr: admin psw: monitorpassword
	Al conectarme desde grafana ha hecho falta unir grafana a la red de prometheus, para que 
	el contenedor sea capaz de ver a prometheus. Y al setear la connexión no se hace usando 
	localhost::9090, por que realmente localhost en este caso es grafana, y no mi máquina, con lo
	que localhost:9090 no apunta a prometheus. En lugar de eso hay que usar http://prometheus:9090
	con lo que nos conectamos a la red de prometheus.



