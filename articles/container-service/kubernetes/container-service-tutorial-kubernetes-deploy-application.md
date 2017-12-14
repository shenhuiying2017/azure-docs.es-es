---
title: "Tutorial de Azure Container Service: implementación de una aplicación"
description: "Tutorial de Azure Container Service: implementación de una aplicación"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c763d6867deb76f5b9d197c7062ee07d7ed6d865
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="run-applications-in-kubernetes"></a>Ejecución de aplicaciones en Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

En este tutorial, la cuarta parte de siete, se implementa una aplicación de ejemplo en un clúster de Kubernetes. Los pasos completados incluyen:

> [!div class="checklist"]
> * Actualización de los archivos de manifiesto de Kubernetes
> * Ejecución de una aplicación en Kubernetes
> * Prueba de la aplicación

En tutoriales posteriores se escala horizontalmente y se actualiza esta aplicación, y se configura Operations Management Suite para supervisar el clúster de Kubernetes.

En este tutorial se supone que tiene unos conocimientos básicos de los conceptos de Kubernetes. Para obtener una información más detallada sobre esta aplicación, consulte la [documentación de Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se empaquetó una aplicación en una imagen de contenedor, se cargó esta imagen en Azure Container Registry y se creó un clúster de Kubernetes. 

Para completar este tutorial, necesita el archivo de manifiesto de Kubernetes `azure-vote-all-in-one-redis.yml` creado previamente. Este archivo se descargó con el código fuente de la aplicación en un tutorial anterior. Confirme que ha clonado el repositorio y que ha cambiado los directorios en el repositorio clonado.

Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service). 

## <a name="update-manifest-file"></a>Actualización del archivo de manifiesto

En este tutorial se ha usado Azure Container Registry (ACR) para almacenar una imagen de contenedor. Antes de ejecutar la aplicación, hay que actualizar el nombre del servidor de inicio de sesión de ACR en el archivo de manifiesto de Kubernetes.

El nombre del servidor de inicio de sesión de ACR se obtiene con el comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

El archivo de manifiesto se creó previamente con el nombre de servidor de inicio de sesión `microsoft`. Abra el archivo con un editor de texto. En este ejemplo, el archivo se abre con `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Reemplace `microsoft` por el nombre del servidor de inicio de sesión de ACR. Este valor se encuentra en la línea **47** del archivo de manifiesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Guarde y cierre el archivo.

## <a name="deploy-application"></a>Implementación de la aplicación

Use el comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para ejecutar la aplicación. Este comando analiza el archivo de manifiesto y crea los objetos de Kubernetes definidos.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Salida:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Prueba de la aplicación

Se crea un [servicio de Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) que expone la aplicación a Internet. Este proceso puede tardar unos minutos. 

Para supervisar el progreso, utilice el comando [kubectl get service](https://review.docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) con el argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

En un primer momento, el parámetro **EXTERNAL-IP** del servicio `azure-vote-front` aparece como `pending`. Cuando el parámetro EXTERNAL-IP haya cambiado de `pending` a una `IP address`, use `CTRL-C` para detener el proceso de inspección de kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Para ver la aplicación, navegue hasta la dirección IP externa.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha implementado la aplicación Azure Vote en un clúster de Kubernetes de Azure Container Service. Las tareas completadas incluyen:  

> [!div class="checklist"]
> * Descargar archivos de manifiesto de Kubernetes
> * Ejecutar la aplicación en Kubernetes
> * Probar la aplicación

Pase al siguiente tutorial para obtener información acerca de cómo escalar una aplicación Kubernetes y la infraestructura de Kubernetes subyacente. 

> [!div class="nextstepaction"]
> [Escalar infraestructura y aplicación de Kubernetes](./container-service-tutorial-kubernetes-scale.md)