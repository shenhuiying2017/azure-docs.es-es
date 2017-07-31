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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Creación de imágenes de contenedor que se van a usar con Azure Container Service

En este tutorial, se prepara una aplicación para Kubernetes. Los pasos completados incluyen:  

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de imágenes de contenedor desde el origen de la aplicación
> * La prueba las imágenes en un entorno local de Docker

En los tutoriales posteriores, estas imágenes de contenedor se cargan en una instancia de Azure Container Registry y después se ejecutan en un clúster de Kubernetes hospedado en Azure.

## <a name="before-you-begin"></a>Antes de empezar

En este tutorial se supone que el usuario tiene un conocimiento básico de los principales conceptos de Docker, como los contenedores, las imágenes de contenedor y los comandos básicos de Docker. Si es necesario, consulte la [introducción a Docker]( https://docs.docker.com/get-started/), donde encontrará datos básicos acerca de los contenedores. 

Para completar este tutorial, se necesita un entorno de desarrollo de Docker. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obtención del código de la aplicación

La aplicación de ejemplo que se usa en este tutorial es una aplicación básica para votar. La aplicación consta de un componente web front-end web y una base de datos back-end. 

Use git para descargar una copia de la aplicación en su entorno de desarrollo.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

En el directorio de aplicación, se pueden encontrar Dockerfiles y archivos de manifiesto de Kubernetes creados previamente. Estos archivos se utilizan para crear recursos en el conjunto del tutorial. 

## <a name="create-container-images"></a>Creación de imágenes de contenedor

Para crear una imagen de contenedor para el front-end de la aplicación, use el comando [docker build](https://docs.docker.com/engine/reference/commandline/build/).

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

Repita el comando, esta vez para la imagen de contenedor de back-end.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

Cuando haya finalizado, use el comando `docker images` para ver las imágenes creadas. 

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>Prueba de la aplicación

Ahora que se han creado dos imágenes de contenedor, pruébelas en el entorno de desarrollo local. 

En primer lugar, cree una red de Docker. Esta red se usa para la comunicación entre los contenedores.  

```bash
docker network create azure-vote
```

Ejecute una instancia de la imagen de contenedor de back-end mediante el comando `docker run`.

En este ejemplo, el archivo de base de datos de mysql se almacena en el contenedor. Una vez que esta aplicación se mueve a los clústeres de Kubernetes, se usa un volumen de datos externos se usa para almacenar el archivo de base de datos. Además, se usan variables de entorno para establecer las credenciales de MySQL.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Ejecute una instancia de la imagen de contenedor de front-end.

Las variables de entorno se usan para configurar la información de conexión de la base de datos.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

Cuando haya terminado, ejecute `docker ps` para ver los contenedores en ejecución.  

```bash
docker ps
```

Salida:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

Vaya a `http://localhost:8080` para ver la aplicación en ejecución. La aplicación tarda unos segundos en inicializarse. Si se produce un error, vuelva a intentarlo.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Ahora que la funcionalidad de la aplicación se ha validado, los contenedores en ejecución se pueden detener y eliminar. No elimine las imágenes del contenedor. Dichas imágenes se cargan en una instancia de Azure Container Registry en el siguiente tutorial.

Detenga y elimine el contenedor front-end con el comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-front
```

Detenga y elimine el contenedor back-end con el comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-back
```

Elimine la red con el comando [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/).

```bash
docker network rm azure-vote
```

AL finalizar, tendrá dos imágenes de contenedor que componen la aplicación Azure Vote.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha probado una aplicación y se han creado imágenes del contenedor para la aplicación. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de imágenes de contenedor desde el origen de la aplicación
> * La prueba las imágenes en un entorno local de Docker

Pase al siguiente tutorial para aprender a almacenar imágenes de contenedor en Azure Container Registry.

> [!div class="nextstepaction"]
> [Insertar imágenes en Azure Container Registry](./container-service-tutorial-kubernetes-prepare-acr.md)
