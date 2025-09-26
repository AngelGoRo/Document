#-------------------
## 1. Redis
#-------------------

# variables
REDIS_TAG = 3
REDIS_CONTAINER_NAME = redis
REDIS_IMAGE_NAME = redis

# Construcción y despliegue
build_redis:
	@echo 'Construccion de redis con el tag=$(REDIS_TAG)'
	docker pull $(REDIS_IMAGE_NAME):$(REDIS_TAG)
	@echo 'Levantamos el contenedor de $(REDIS_IMAGE_NAME):$(REDIS_TAG)'
	docker run -d --name $(REDIS_CONTAINER_NAME) $(REDIS_IMAGE_NAME):$(REDIS_TAG)
	
# @mkdir carpetita # esto crearía una carpetita
# Al ejecutar el comando se puede añadir un valor nuevo para una de las variables, de 
# modo que se cambia la ejecución, ej: make build_redis REDIS_TAG=2 

#------------------------
# Eliminar el contenedor de redis
#------------------------

delete_redis:
	@echo 'Parar redis'
	docker stop $(REDIS_CONTAINER_NAME)
	@echo 'eliminacion'
	docker rm $(REDIS_CONTAINER_NAME)
	@echo 'eliminacion de la imagenes'
	docker rmi $(REDIS_IMAGE_NAME):$(REDIS_TAG)
	$(MAKE) show_all

#-------------------
## 2. Despliegue del monitor
#-------------------

build_monitor_prometheus:
	@echo 'Construcion y despliegue de prometheus'
	cd monitor/prometheus && docker-compose up -d

build_monitor_redis:
	@echo 'Construcion y despliegue de redis'
	cd monitor/redis && docker-compose up -d

build_monitor_grafana:
	@echo 'Construcion y despliegue de grafana'
	cd monitor/grafana && docker-compose up -d

build_monitor_redis_exporter:
	@echo 'Construcion y despliegue de redis_exporter'
	cd monitor/redis-exporter && docker-compose up -d

build_monitor: 	## Levantar los servicios
	@echo 'Levantamos todos los servicios'
	$(MAKE) build_monitor_prometheus
	$(MAKE) build_monitor_redis
	$(MAKE) build_monitor_grafana
	$(MAKE) build_monitor_redis_exporter
	@echo '--- contenedores en ejecucion ---'
	docker ps -a

# Parar los servicios

stop_monitor_prometheus:
	@echo 'Paramos el servicio de prometheus'
	cd monitor/prometheus && docker-compose stop

stop_monitor_redis:
	@echo 'Paramos el servicio de redis'
	cd monitor/redis && docker-compose stop

stop_monitor_grafana:
	@echo 'Paramos el servicio de grafana'
	cd monitor/grafana && docker-compose stop

stop_monitor_redis_exporter:
	@echo 'Paramos el servicio de redis_exporter'
	cd monitor/redis-exporter && docker-compose stop

stop_monitor: 	## Parar los servicios
	@echo 'Apagamos todos los servicios'
	$(MAKE) stop_monitor_prometheus
	$(MAKE) stop_monitor_redis
	$(MAKE) stop_monitor_grafana
	$(MAKE) stop_monitor_redis_exporter
	@echo '--- contenedores en ejecucion ---'
	docker ps -a

# Parar los servicios

down_monitor_prometheus:
	@echo 'Eliminamos el servicio de prometheus'
	cd monitor/prometheus && docker-compose down

down_monitor_redis:
	@echo 'Eliminamos el servicio de redis'
	cd monitor/redis && docker-compose down

down_monitor_grafana:
	@echo 'Eliminamos el servicio de grafana'
	cd monitor/grafana && docker-compose down

down_monitor_redis_exporter:
	@echo 'Eliminamos el servicio de redis_exporter'
	cd monitor/redis-exporter && docker-compose down

down_monitor: 	## Eliminar los servicios
	@echo 'Eliminamos todos los servicios'
	$(MAKE) down_monitor_prometheus
	$(MAKE) down_monitor_redis
	$(MAKE) down_monitor_grafana
	$(MAKE) down_monitor_redis_exporter
	@echo '--- contenedores en ejecucion ---'
	docker ps -a

#-------------------
## 2. Despliegue de ELK
#-------------------

build_ELK:	## Construcción y despliegue de ELK
	@echo 'Construcción de ELK'
	cd ELK && docker-compose build
	@echo 'Levantamos ELK'
	cd ELK && docker-compose up -d

stop_ELK:	## Parar el servicio de ELK
	@echo 'Parar el servicio de ELK'
	cd ELK && docker-compose stop

start_ELK:	## Iniciar el servicio de ELK
	@echo 'Iniciar el servicio de ELK'
	cd ELK && docker-compose start

restart_ELK:	## Reiniciar el servicio de ELK
	@echo 'Reiniciar el servicio de ELK'
	cd ELK && docker-compose restart

down_ELK:	## Eliminación profunda ELK
	$(MAKE) show_all
	@echo 'Eliminación el servicio de ELK'
	cd ELK && docker-compose down	
	$(MAKE) show_all

#------------------------
## 999. Comandos generales
#------------------------

-PHONY: help
help:	## Comando de ayuda
	powershell -ExecutionPolicy Bypass -File .\Help.ps1

show_containers: ## visualización de contenedores e imagenes de docker
	@echo --- Contenedores en EJECUCION ---
	docker ps --filter status=running
	@echo --- Contenedores PARADOS --- 
	docker ps --filter status=exited


show_images: ## visualización de contenedores e imagenes de docker
	@echo --- Imagenes --- 
	docker images

show_all: ## visualización de contenedores e imagenes de docker
	$(MAKE) show_containers
	$(MAKE) show_images

#help: # Comando de ayuda solo para linux
#@fgrep -h "#" %(MAKEFILE_LIST) | fgrep -v fgrep | sed -d 's/\\$$//' | sed -e 's/##//' 

# variables
N1 = Angel Gomez
N2 = David oOmez
NOMBRE = default
REDIS_IMAGE_NAME = redis

# Construcción y despliegue
ver_nombre:
	@echo 'El nombre que tenemos es $($(NOMBRE))'