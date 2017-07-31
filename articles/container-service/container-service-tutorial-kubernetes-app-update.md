---
title: "Tutorial de Azure Container Service: actualización de una aplicación | Microsoft Docs"
description: "Tutorial de Azure Container Service: actualización de una aplicación"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Actualización de una aplicación en Kubernetes

Después de implementar una aplicación en Kubernetes, puede actualizarla mediante la especificación de una nueva imagen de contenedor o la versión de la imagen. Cuando se actualiza una aplicación, el lanzamiento de la actualización se preconfigura para que solo una parte de la implementación se actualice simultáneamente. 

Esta actualización preconfigurada permite que la aplicación siga ejecutándose durante la actualización y proporciona un mecanismo de reversión si se produce un error de implementación. 

En este tutorial, se actualiza la aplicación de ejemplo de Azure Vote. Las tareas que debe completar incluyen las siguientes:

> [!div class="checklist"]
> * Actualización del código de aplicación front-end.
> * Creación de una imagen de contenedor actualizado.
> * Inserción de una imagen de contenedor en Azure Container Registry.
> * Implementación de una aplicación actualizada.

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores se empaquetó una aplicación en imágenes de contenedor, se cargaron estas imágenes en Azure Container Registry y se creó un clúster de Kubernetes. Después, ejecutamos la aplicación en el clúster de Kubernetes. 

Si aún no se han seguido estos pasos y desea probarlos ahora, vuelva al [tutorial 1: creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md). 

Como mínimo, este tutorial requiere un clúster de Kubernetes con una aplicación en ejecución.

## <a name="update-application"></a>Actualización de una aplicación

Para completar los pasos de este tutorial, debe clonar una copia de la aplicación Azure Vote. Si es necesario, cree esta copia clonada con el siguiente comando:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Abra el archivo `config_file.cfg` con cualquier editor de texto o código. Puede encontrar este archivo en el siguiente directorio del repositorio clonado.

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

Cambie los valores de `VOTE1VALUE` y `VOTE2VALUE` y, después, guarde el archivo.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

Use `docker build` para volver a crear la imagen de front-end.

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>Prueba de la aplicación

Cree una red de Docker. Esta red se usa para la comunicación entre los contenedores.  

```bash
docker network create azure-vote
```

Ejecute una instancia de la imagen de contenedor de back-end mediante el comando `docker run`.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Ejecute una instancia de la imagen de contenedor de front-end.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

Vaya a `http://localhost:8080` para ver la aplicación actualizada. La aplicación tarda unos segundos en inicializarse. Si se produce un error, vuelva a intentarlo.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Etiquetado e inserción de imágenes

Etiquete la imagen *azure-vote-front* con el loginServer del registro de contenedor.

Si usa Azure Container Registry, obtenga el nombre del servidor de inicio de sesión con el comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilice [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para etiquetar la imagen, y asegúrese de actualizar el comando con su servidor de inicio de sesión de Azure Container Registry o con el nombre de host de registro público.

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

Inserte la imagen en el registro. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de Azure Container Registry o por el nombre de host de registro público.

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Implementación de la actualización en Kubernetes

### <a name="verify-multiple-pod-replicas"></a>Comprobación de varias réplicas POD

Para garantizar el tiempo máximo de actividad, se deben ejecutar varias instancias de pod de la aplicación. Compruebe esta configuración con el comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Salida:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Si no tiene varios pods ejecutando la imagen de azure-vote-front, escale la implementación de *azure-vote-front*.


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>Actualización de una aplicación

Para actualizar la aplicación, ejecute el comando siguiente. Actualice `<acrLoginServer>` con el nombre de host o servidor de inicio de sesión del registro de contenedor.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Para supervisar la implementación, use el comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). A medida que se implementa la aplicación actualizada, sus pods se terminan y se vuelven a crear con la nueva imagen de contenedor.

```bash
kubectl get pod
```

Salida:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Prueba de la aplicación actualizada

Obtenga la dirección IP externa del servicio *azure -vote-front*.

```bash
kubectl get service
```

Vaya a la dirección IP para ver la aplicación actualizada.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se actualiza una aplicación y se implanta esta actualización en un clúster de Kubernetes. Hemos completado las siguientes tareas:  

> [!div class="checklist"]
> * Se ha actualizado el código de aplicación de front-end.
> * Se ha creado la imagen de contenedor actualizado.
> * Se ha insertado una imagen de contenedor en Azure Container Registry.
> * Se ha implementado la aplicación actualizada.

Avance al siguiente tutorial para obtener información sobre cómo supervisar Kubernetes con Operations Management Suite.

> [!div class="nextstepaction"]
> [Supervisión de Kubernetes con OMS](./container-service-tutorial-kubernetes-monitor.md)
