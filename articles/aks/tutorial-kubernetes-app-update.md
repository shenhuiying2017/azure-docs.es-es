---
title: "Tutorial de Kubernetes en Azure: actualización de la aplicación"
description: "Tutorial de AKS: actualización de la aplicación"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6de5173aedc836f7a2d56370ea8e54ad6e77ab5e
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="update-an-application-in-azure-container-service-aks"></a>Actualización de una aplicación en Azure Container Service (AKS)

Después de implementar una aplicación en Kubernetes, se puede actualizar especificando una nueva imagen de contenedor o la versión de la imagen. Si lo hace, la actualización se preconfigura para que solo una parte de la implementación se actualice simultáneamente. Esta actualización preconfigurada permite que la aplicación siga ejecutándose durante la actualización. También proporciona un mecanismo de reversión si se produce un error de implementación. 

En este tutorial, la sexta parte de ocho, se actualiza la aplicación de ejemplo Azure Vote. Las tareas que debe completar incluyen las siguientes:

> [!div class="checklist"]
> * Actualización del código de aplicación front-end
> * Creación de una imagen de contenedor actualizado
> * Inserción de una imagen de contenedor en Azure Container Registry
> * Implementación de una imagen de contenedor actualizado

En tutoriales posteriores, se configura Operations Management Suite para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, se ha cargado la imagen en Azure Container Registry y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes. 

También se clonó un repositorio de aplicación que incluye el código fuente de la aplicación y un archivo de Docker Compose creado previamente que se usa en este tutorial. Confirme que ha creado un clon del repositorio y que ha cambiado los directorios en el repositorio clonado. Dentro hay un directorio denominado `azure-vote` y un archivo denominado `docker-compose.yaml`.

Si no ha finalizado estos pasos y desea continuar, vuelva al [Tutorial 1: Creación de las imágenes de contenedor][aks-tutorial-prepare-app]. 

## <a name="update-application"></a>Actualización de una aplicación

En este tutorial, se realiza un cambio en la aplicación y la aplicación actualizada se implementa en el clúster de Kubernetes. 

El código fuente de la aplicación está en el directorio `azure-vote`. Abra el archivo `config_file.cfg` con cualquier editor de texto o código. En este ejemplo se usa `vi` .

```console
vi azure-vote/azure-vote/config_file.cfg
```

Cambie los valores de `VOTE1VALUE` y `VOTE2VALUE` y, después, guarde el archivo.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde y cierre el archivo.

## <a name="update-container-image"></a>Actualizar una imagen de contenedor

Use [docker-compose][docker-compose] para volver a crear la imagen de front-end y ejecutar la aplicación actualizada. El argumento `--build` se emplea para indicar a Docker Compose que vuelva a crear la imagen de la aplicación.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Prueba local de la aplicación

Vaya a http://localhost:8080 para ver la aplicación actualizada.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Etiquetado e inserción de imágenes

Etiquete la imagen `azure-vote-front` con el loginServer del registro de contenedor. 

El nombre del servidor de inicio de sesión se obtiene con el comando [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use [docker tag][docker-tag] para etiquetar la imagen. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de Azure Container Registry o por el nombre de host de registro público. Observe también que la versión de la imagen se ha actualizado a `redis-v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Use [docker push][docker-push] para cargar la imagen en el registro. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de Azure Container Registry.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Implementación de una aplicación actualizada

Para garantizar el tiempo máximo de actividad, se deben ejecutar varias instancias de pod de la aplicación. Compruebe esta configuración con el comando [kubectl get pod][kubectl-get].

```
kubectl get pod
```

Salida:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Si no tiene varios pods ejecutando la imagen de azure-vote-front, escale la implementación de `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Use el comando [kubectl set][kubectl-set] para actualizar la aplicación. Actualice `<acrLoginServer>` con el nombre de host o servidor de inicio de sesión del registro de contenedor.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Para supervisar la implementación, use el comando [kubectl get pod][kubectl-get]. A medida que se implementa la aplicación actualizada, sus pods se terminan y se vuelven a crear con la nueva imagen de contenedor.

```azurecli
kubectl get pod
```

Salida:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Prueba de la aplicación actualizada

Obtenga la dirección IP externa del servicio `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Navegue hasta la dirección IP para ver la aplicación actualizada.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>pasos siguientes

En este tutorial, actualiza una aplicación y se implanta esta actualización en un clúster de Kubernetes. Se completaron las siguientes tareas:

> [!div class="checklist"]
> * Actualizó el código de aplicación de front-end
> * Creó la imagen de contenedor actualizado
> * Insertó una imagen de contenedor en Azure Container Registry
> * Implemento la aplicación actualizada

Avance al siguiente tutorial para obtener información sobre cómo supervisar Kubernetes con Operations Management Suite.

> [!div class="nextstepaction"]
> [Supervisar Kubernetes con Log Analytics][aks-tutorial-monitor]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-monitor]: ./tutorial-kubernetes-monitor.md