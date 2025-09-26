Comandos que vamos usando:

Creación del contenedor que va a correr MySQL, establecemos la red de cocker "todo-app" con el alias "mysql"
y hacemos que se almacene en una carpeta en el host, seteamos una contraseña y un nombre para una base de datos
que va a correr en el contenedor al iniciarse este

>> docker run -d `
 --network todo-app --network-alias mysql `
 -v "C:\Proyectos\Curso Docker\multi-container\todo-mysql-data:/var/lib/mysql" `
 -e MYSQL_ROOT_PASSWORD=my-secret-pw `
 -e MYSQL_DATABASE=todos `
 mysql:8.0.42-debian   (5.7)


Creamos la red de docker por que antes no existía, realmente el comando anterior ha dado
fallo por que no hemos creado previamente la red.

>> docekr network create todo-app


Nos conectamos al contenedor para ver la base de datos en funcionamiento y comprobar que 
es correcto.

>> docker exec -it [ID_contenedor]  mysql -p


Ejecutamos el contenedor que corre nuestra app que es el que va a hacer uso de la 
base de datoss Mysql

>> docker run -dp 3000:3000 `
--network todo-app `
-e MYSQL_HOST=mysql `
-e MYSQL_USER=root `
-e MYSQL_PASSWORD=my-secret-pw `
-e MYSQL_DB=todos `
my_app:v2

Este comando me estaba fallando por que my_app usaba una versión de mysql antigua con respecto a la que yo había utilizado (yo he usado la ultima versión) y por ello el contenedor crasheaba al crearse. Para solucionarlo he tenido que descargar la imagen corres
pondiente a la versión que en este caso era la 5.7 y luego borrar toda la base de datos que se había creado anteriormente en el directorio vinculado con -v por que al crear la nueva base de datos volvía a romper al estar ya la versión actualizada en ese lugar.

