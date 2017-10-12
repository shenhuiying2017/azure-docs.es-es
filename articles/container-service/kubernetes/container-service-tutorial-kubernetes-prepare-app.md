---
title: "Tutorial de Azure Container Service: preparación de una aplicación | Microsoft Docs"
description: "Tutorial de Azure Container Service: preparación de una aplicación"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d09f59715ac4dac1482e6ca5f79e8426f1f01ced
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Creación de imágenes de contenedor que se van a usar con Azure Container Service

En este tutorial, la primera parte de siete, se prepara una aplicación con varios contenedores para usarla en Kubernetes. Los pasos completados incluyen:  

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de una imagen de contenedor desde el origen de la aplicación
> * La prueba de la aplicación en un entorno local de Docker

Tras completar los pasos anteriores, se puede acceder a la siguiente aplicación en el entorno de desarrollo local.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

En los tutoriales posteriores, la imagen de contenedor se carga en una instancia de Azure Container Registry y después se ejecuta en un clúster de Kubernetes hospedado en Azure.

## <a name="before-you-begin"></a>Antes de empezar

En este tutorial se supone que el usuario tiene un conocimiento básico de los principales conceptos de Docker, como los contenedores, las imágenes de contenedor y los comandos básicos de Docker. Si es necesario, consulte la [introducción a Docker]( https://docs.docker.com/get-started/), donde encontrará datos básicos acerca de los contenedores. 

Para completar este tutorial, se necesita un entorno de desarrollo de Docker. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Por lo tanto, se recomienda usar un entorno completo de desarrollo de Docker.

## <a name="get-application-code"></a>Obtención del código de la aplicación

La aplicación de ejemplo que se usa en este tutorial es una aplicación básica para votar. La aplicación consta de un componente web front-end y de una instancia back-end de Redis. El componente web se empaqueta en una imagen de contenedor personalizada. La instancia de Redis usa una imagen sin modificar de Docker Hub.  

Use git para descargar una copia de la aplicación en su entorno de desarrollo.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Cambie los directorios para trabajar desde el directorio clonado.

```
cd azure-voting-app-redis
```

En el directorio se encuentra el código fuente de la aplicación, un archivo de Docker Compose creado previamente y un archivo de manifiesto de Kubernetes. Estos archivos se usan en todo el conjunto del tutorial. 

## <a name="create-container-images"></a>Creación de imágenes de contenedor

[Docker Compose](https://docs.docker.com/compose/) puede utilizarse para automatizar la creación de imágenes de contenedor y la implementación de aplicaciones con varios contenedores.

Ejecute el archivo `docker-compose.yml` para crear la imagen de contenedor, descargue la imagen de Redis e inicie la aplicación.

```bash
docker-compose up -d
```

Cuando haya finalizado, use el comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver las imágenes creadas.

```bash
docker images
```

Tenga en cuenta que se han descargado o creado tres imágenes. La imagen `azure-vote-front` contiene la aplicación y usa la imagen `nginx-flask` como base. La imagen `redis` se usa para iniciar una instancia de Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Ejecute el comando [docker pss](https://docs.docker.com/engine/reference/commandline/ps/) para ver los contenedores en ejecución.

```bash
docker ps
```

Salida:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Prueba local de la aplicación

Vaya a http://localhost:8080 para ver la aplicación en ejecución.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Ahora que la funcionalidad de la aplicación se ha validado, los contenedores en ejecución se pueden detener y eliminar. No elimine las imágenes del contenedor. La imagen `azure-vote-front` se carga en una instancia de Azure Container Registry en el siguiente tutorial.

Ejecute el siguiente comando para detener los contenedores en ejecución.

```bash
docker-compose stop
```

Elimine los recursos y contenedores detenidos con el siguiente comando.

```bash
docker-compose down
```

Al finalizar, tendrá una imagen de contenedor que contiene la aplicación Azure Vote.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha probado una aplicación y se han creado imágenes del contenedor para la aplicación. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de una imagen de contenedor desde el origen de la aplicación
> * La prueba de la aplicación en un entorno local de Docker

Pase al siguiente tutorial para aprender a almacenar imágenes de contenedor en Azure Container Registry.

> [!div class="nextstepaction"]
> [Insertar imágenes en Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
