---
title: "Administración de clústeres de Azure Swarm con la API de Docker | Microsoft Docs"
description: "Implementación de contenedores en un clúster de Docker Swarm en Azure Container Service"
services: container-service
documentationcenter: 
author: rgardler
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6ca2d2e49c4b7f5eb0580e7091b09209f8b73a7c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="container-management-with-docker-swarm"></a>Administración de contenedores con Docker Swarm
Docker Swarm proporciona un entorno para implementar cargas de trabajo en contenedores a través de un conjunto agrupado de hosts de Docker. Docker Swarm usa la API nativa de Docker. El flujo de trabajo para administrar contenedores en un Docker Swarm es casi idéntico al que sería en un host de un solo contenedor. Este documento proporciona ejemplos sencillos de la implementación de cargas de trabajo en contenedores en una instancia de Azure Container Service (servicio Contenedor de Azure) de Docker Swarm. Para obtener documentación más detallada sobre Docker Swarm, consulte [Docker Swarm en Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Requisitos previos para los ejercicios de este documento:

[Crear un clúster de Swarm en Azure Container Service (servicio Contenedor de Azure)](container-service-deployment.md)

[Conectar con el clúster de Swarm en Azure Container Service (servicio Contenedor de Azure)](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Implementación de un contenedor nuevo
Para crear un nuevo contenedor en Docker Swarm, use el comando `docker run` (asegúrese de que ha abierto un túnel SSH para los patrones de acuerdo con los requisitos previos anteriores). Este ejemplo crea un contenedor a partir de la imagen `yeasy/simple-web` :

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Una vez creado el contenedor, utilice `docker ps` para devolver información acerca del mismo. Observe que aparece el agente de Swarm que hospeda el contenedor:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Ahora puede acceder a la aplicación que se ejecuta en este contenedor a través del nombre DNS público del equilibrador de carga del agente de Swarm. Puede encontrar esta información en el Portal de Azure:  

![Resultados de la visita real](./media/container-service-docker-swarm/real-visit.jpg)  

De forma predeterminada, el equilibrador de carga tiene los puertos 80, 443 y 8080 abiertos. Si va a conectar en otro puerto debe abrirlo en Azure Load Balancer para el grupo de agentes.

## <a name="deploy-multiple-containers"></a>Implementación de varios contenedores
Cuando se inician varios contenedores, al ejecutar varias veces "docker run", se puede usar el comando `docker ps` para ver en qué host se ejecutan los contenedores. En el siguiente ejemplo, tres contenedores se propagan uniformemente por los tres agentes de Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Implementación de contenedores mediante Docker Compose
Puede usar Docker Compose para automatizar la implementación y configuración de varios contenedores. Para ello, asegúrese de que se ha creado un túnel Secure Shell (SSH) y se ha establecido la variable DOCKER_HOST (consulte los requisitos previos anteriores).

Cree un archivo docker-compose.yml en el sistema local. Para ello, use este [ejemplo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

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

Ejecute `docker-compose up -d` para iniciar las implementaciones de contenedores:

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

Por último, se devolverá la lista de contenedores en ejecución. Esta lista refleja los contenedores que se implementaron mediante Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalmente, puede usar `docker-compose ps` para examinar solo los contenedores definidos en su archivo `compose.yml`.

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de Docker Swarm.](https://docs.docker.com/swarm/)

