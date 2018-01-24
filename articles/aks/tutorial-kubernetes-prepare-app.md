---
title: "Tutorial de Kubernetes en Azure: preparación de aplicación"
description: "Tutorial de AKS: preparación de aplicación"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 60e0feb1e45ac5d9f35eac9667eaf9004d77e86a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="prepare-application-for-azure-container-service-aks"></a>Preparación de una aplicación en Azure Container Service (AKS)

En este tutorial, la primera parte de ocho, se prepara una aplicación con varios contenedores para usarla en Kubernetes. Los pasos completados incluyen:  

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de una imagen de contenedor desde el origen de la aplicación
> * La prueba de la aplicación en un entorno local de Docker

Tras completar los pasos anteriores, se puede acceder a la siguiente aplicación en el entorno de desarrollo local.

![Imagen del clúster de Kubernetes en Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

En los tutoriales posteriores, la imagen de contenedor se carga en una instancia de Azure Container Registry y después se ejecuta en un clúster de AKS.

## <a name="before-you-begin"></a>Antes de empezar

En este tutorial se supone que el usuario tiene un conocimiento básico de los principales conceptos de Docker, como los contenedores, las imágenes de contenedor y los comandos básicos de Docker. Si es necesario, consulte la [introducción a Docker][docker-get-started], donde encontrará datos básicos acerca de los contenedores. 

Para completar este tutorial, se necesita un entorno de desarrollo de Docker. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-for-mac], [Windows][docker-for-windows] o [Linux][docker-for-linux].

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Por lo tanto, se recomienda usar un entorno completo de desarrollo de Docker.

## <a name="get-application-code"></a>Obtención del código de la aplicación

La aplicación de ejemplo que se usa en este tutorial es una aplicación básica para votar. La aplicación consta de un componente web front-end y de una instancia back-end de Redis. El componente web se empaqueta en una imagen de contenedor personalizada. La instancia de Redis usa una imagen sin modificar de Docker Hub.  

Use git para descargar una copia de la aplicación en su entorno de desarrollo.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Cambie los directorios para trabajar desde el directorio clonado.

```console
cd azure-voting-app-redis
```

En el directorio se encuentra el código fuente de la aplicación, un archivo de Docker Compose creado previamente y un archivo de manifiesto de Kubernetes. Estos archivos se usan en todo el conjunto del tutorial. 

## <a name="create-container-images"></a>Creación de imágenes de contenedor

[Docker Compose][docker-compose] puede utilizarse para automatizar la creación de imágenes de contenedor y la implementación de aplicaciones con varios contenedores.

Ejecute el archivo `docker-compose.yaml` para crear la imagen de contenedor, descargue la imagen de Redis e inicie la aplicación.

```console
docker-compose up -d
```

Cuando haya finalizado, use el comando [docker images][docker-images] para ver las imágenes creadas.

```console
docker images
```

Tenga en cuenta que se han descargado o creado tres imágenes. La imagen `azure-vote-front` contiene la aplicación y usa la imagen `nginx-flask` como base. La imagen `redis` se usa para iniciar una instancia de Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Ejecute el comando [docker pss][docker-ps] para ver los contenedores en ejecución.

```console
docker ps
```

Salida:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Prueba local de la aplicación

Vaya a http://localhost:8080 para ver la aplicación en ejecución.

![Imagen del clúster de Kubernetes en Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Ahora que la funcionalidad de la aplicación se ha validado, los contenedores en ejecución se pueden detener y eliminar. No elimine las imágenes del contenedor. La imagen `azure-vote-front` se carga en una instancia de Azure Container Registry en el siguiente tutorial.

Ejecute el siguiente comando para detener los contenedores en ejecución.

```console
docker-compose stop
```

Elimine los recursos y contenedores detenidos con el siguiente comando.

```console
docker-compose down
```

Al finalizar, tendrá una imagen de contenedor que contiene la aplicación Azure Vote.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, se ha probado una aplicación y se han creado imágenes del contenedor para la aplicación. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * La clonación de origen de la aplicación desde GitHub  
> * La creación de una imagen de contenedor desde el origen de la aplicación
> * La prueba de la aplicación en un entorno local de Docker

Pase al siguiente tutorial para aprender a almacenar imágenes de contenedor en Azure Container Registry.

> [!div class="nextstepaction"]
> [Insertar imágenes en Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md