# Comandos Docker compose

Para ejecutar un  docker compose se necesita crear un archivo .yml que tendrá el nombre "dockercompose.yml" este será el que almacene la información de las .

## Comandos

Antes de introducir ninguno de los comandos perteneciente a cada uno de los servicios debemos declarar la etiqueta services. Posteriormente cada uno de los servicios se identifdicará con su correspondiente etiqueta que llebará su nombre. 

Estos se declaran individualmente en el siguiente nivel de tabulación.

    services:
        servicio 1:
            declaración...
        servicio 2:
            declaración...

### Imagen

Para especificar la imagen que usar la etiqueta  image, esta determinará la imagen a partir de la cual partirá el servicio.

    services:
        db:
            image : postgres

### Dockerfile

En caso de tener un dockerfile, podemos utilizarlo en lugar de la imagen: 

    services:
        webapp:
            build: ./ubicacion_del_Dockerfile

El ejemplo anterior solo sería valido con un unico dockerfile único, si queremos que el dockerfile tenga un nombre personalizado haríamos:

    services:
        webapp:
            build:
                context: ./ubicacion_del_Dockerfile
                dockerfile: Dockerfile-personalizado

### Sobreescribir un comando:

    services:
        web:
            build: .
                command: python manage.py runserver localhost:8000

### Para eponer un puerto:

Podemos exponerlo para la máquina host:
    ...
    web:
        build: .
        ports:
            - 80:8000

O exponerlo solo para los servicios en red con el propio servicio:

    ...
        web:
            build: .
            export:
                - 80

### Dependencias:

En caso que uno de los servicios sea dependiente de otro. Con el siguiente comando ejecutamos el servicio solo si los servicios dependientes están instalados:

    ...
    depends_on:
      - db
      - redis

### Variables de entorno:

Para setear variables de entrono dentro del servicio:

    ...
    environment:
        - SQL_USER=Angel
        - SQL_PSWD=letmein

o también:

    ...
    environment:
      MODE: development
      DEBUG: 'true'

O mediante fichero:

    ...
    environment:
      env_file: comon.env


