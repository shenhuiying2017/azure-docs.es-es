<properties
   pageTitle="Administración de contenedores del servicio Contenedor de Azure con Docker Swarm | Microsoft Azure"
   description="Implementación de contenedores en Docker Swarm en el servicio Contenedor de Azure"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Administración de contenedores con Docker Swarm

Docker Swarm proporciona un entorno para implementar carga de trabajo en contenedores a través de un conjunto agrupado de hosts de Docker. Docker Swarm usa la API de Docker nativa, y el flujo de trabajo para administrar contenedores en un Docker Swarm es casi idéntico al que sería en un host de un solo contenedor. Este documento proporciona ejemplos sencillos de la implementación de una carga de trabajo en contenedores en una instancia del servicio Contenedor de Azure de Docker Swarm. Para obtener documentación más detallada sobre Docker Swarm, consulte [Docker Swarm en Docker.com](https://docs.docker.com/swarm/).

Requisitos previos para los ejercicios de este documento:

[Crear un clúster de Swarm en el servicio Contenedor de Azure](./container-service-deployment.md)

[Conectar con el clúster de Swarm en el servicio Contenedor de Azure](./container-service-connect.md)

## Implementación de un contenedor nuevo

Para crear un nuevo contenedor en Docker Swarm, use el comando `docker run`. Este ejemplo crea un contenedor a partir de la imagen `yeasy/simple-web`.


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Una vez creado el contenedor, utilice `docker ps` para devolver información acerca del mismo. Observe que se enumera el agente de Swarm, que es el que hospeda el contenedor.


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Ahora es posible acceder a la aplicación que se ejecuta en este contenedor a través del nombre DNS público del equilibrador de carga del agente de Swarm. Esta información puede encontrarse en el Portal de Azure.


![](media/real-visit.jpg)

## Implementación de varios contenedores

Cuando se inician varios contenedores en el clúster de Docker Swarm, se puede usar el comando `docker ps` para ver en qué host se ejecutan los contenedores. En este ejemplo, tres contenedores a se propagan uniformemente por los tres agentes de Swarm.


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Implementación de contenedores mediante Docker Compose

Docker Compose puede utilizarse para automatizar la implementación y configuración de varios contenedores. Para ello, asegúrese de que se ha creado un túnel SSH y que se ha establecido la variable DOCKER\_HOST.

Cree un archivo docker-compose.yml en el sistema local. [Aquí](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml) se puede encontrar un ejemplo.

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Ejecute `docker-compose up -d` para iniciar las implementaciones de contenedores.


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por último, se puede devolver la lista de contenedores en ejecución, lo que reflejará los que se han implementado con Docker Compose.


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## Pasos siguientes:

[Más información acerca de Docker Swarm](https://docs.docker.com/swarm/).

<!---HONumber=AcomDC_0420_2016-->