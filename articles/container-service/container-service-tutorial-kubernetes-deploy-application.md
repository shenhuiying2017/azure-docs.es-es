---
title: "Tutorial de Azure Container Service: implementación de una aplicación | Microsoft Docs"
description: "Tutorial de Azure Container Service: implementación de una aplicación"
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
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 445f905a2905d049964fb59bd7a5a7826dd1e700
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="run-applications-in-kubernetes"></a>Ejecución de aplicaciones en Kubernetes

En este tutorial, se implementa una aplicación de ejemplo en un clúster de Kubernetes. Los pasos completados incluyen:

> [!div class="checklist"]
> * Introducción a los objetos de Kubernetes
> * Descargar archivos de manifiesto de Kubernetes
> * Ejecución de una aplicación en Kubernetes
> * Prueba de la aplicación

En los tutoriales posteriores, esta aplicación se escala horizontalmente, se actualizar y el clúster de Kubernetes se supervisa.

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores se empaquetaba una aplicación en imágenes de contenedor, se cargaban estas imágenes en Azure Container Registry y se creó un clúster de Kubernetes. Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service). 

Como mínimo, este tutorial requiere un clúster de Kubernetes.

## <a name="kubernetes-objects"></a>Objetos de Kubernetes

Cuando se implementa una aplicación en contenedor en Kubernetes, se crean muchos objetos de Kubernetes diferentes. Cada objeto representa el estado deseado del clúster. Por ejemplo, una aplicación simple puede constar de un pod, que es una agrupación de contenedores estrechamente relacionados, un volumen persistente, que es un fragmento de almacenamiento en red, y una implementación, que administra el estado de la aplicación. 

Para más información acerca de todos los objetos de Kubernetes, consulte [Kubernetes Concepts](https://kubernetes.io/docs/concepts/) (Conceptos de Kubernetes) en kubernetes.io.

## <a name="get-manifest-files"></a>Obtención de archivos de manifiesto

En este tutorial, los objetos de Kubernetes se implementan mediante manifiestos de Kubernetes. Un manifiesto de Kubernetes es un archivo YAML que contiene instrucciones para la configuración del objeto.

Los archivos de manifiesto de los objetos de este tutorial están disponibles en el repositorio de la aplicación Azure Vote, que se clonó en un tutorial anterior. Si aún no lo ha hecho, clone el repositorio con el siguiente comando: 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Los archivos de manifiesto se encuentran en el siguiente directorio del repositorio clonado.

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>Ejecución de la aplicación

### <a name="storage-objects"></a>Objetos de almacenamiento

Dado que la aplicación Azure Vote incluye una base de datos MySQL, el archivo de base de datos se debe almacenar en un volumen que se pueda compartir entre pods. En esta configuración, si se vuelve a crear el pod de MySQL, el archivo de base de datos no sufrirá ninguna modificación.

El archivo de manifiesto `storage-resources.yaml` crea un [objeto de clase de almacenamiento](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses), que define cómo y dónde se crea un volumen persistente. Hay varios complementos de volumen disponibles para Kubernetes. En este caso, se usa el complemento [disco de Azure](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk). 

También se crea una [notificación de volumen persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims), que configura un fragmento de almacenamiento (mediante una clase de almacenamiento) y lo asigna a un pod.

Ejecute lo siguiente para crear los objetos de almacenamiento.

```bash
kubectl create -f storage-resources.yaml
```

Una vez que la operación finaliza, se crea un disco virtual que se asocia al pod de Kubernetes resultante. El disco virtual se crea automáticamente en una cuenta de almacenamiento que reside en el mismo grupo de recursos que el clúster de Kubernetes y que tiene la misma configuración que el objeto de clase de almacenamiento (Standard_LRS).

### <a name="secure-sensitive-values"></a>Protección de valores confidenciales

Los [secretos de Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) proporcionan almacenamiento seguro para la información confidencial. Mediante el archivo `pod-secrets.yaml`, las credenciales de la base de datos de Azure Vote se almacenan en un secreto. 

Ejecute lo siguiente para crear los objetos de secretos.

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>Creación de implementaciones

Una [implementación de Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) administra el estado de los pods de Kubernetes. Esta administración incluye acciones como asegurarse de que se ejecutan los recuentos de réplicas deseados, que se montan los volúmenes y que se usan las imágenes de contenedor adecuadas.

El archivo de manifiesto `azure-vote-deployment.yaml` crea una implementación para las partes de front-end y back-end de la aplicación Azure Vote.

#### <a name="update-image-names"></a>Actualización de nombres de imágenes

Si se usa Azure Container Registry para almacenar imágenes, delante de los nombres de estas deben incluirse el nombre del servidor de los inicios de sesión de ACR.

El nombre del servidor de inicio de sesión de ACR se obtiene con el comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Actualice los nombres de las imágenes de contenedor *azure-vote-front* y *azure-vote-back* en el archivo `azure-vote-deployment.yaml`.

Ejemplo de nombre de imagen de front-end:

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

Ejemplo de nombre de imagen de back-end:

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>Creación de objetos de implementación

Ejecute [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para iniciar la aplicación Azure Vote.

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>Exponer aplicación

Un [servicio Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) define cómo se accede a un pod. Con la aplicación Azure Vote, el nombre de la implementación debe poder acceder internamente a la implementación de back-end. A la implementación de front-end se debe poder acceder a través de Internet. Las configuraciones de los servicios de la aplicación Azure Vote se definen en el archivo de manifiesto `services.yaml`.

Ejecute lo siguiente para crear los servicios.

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>Prueba de la aplicación

Una vez que se han creado todos los objetos, se puede acceder a la aplicación a través de la dirección IP externa del servicio azure-vote-front. Este servicio puede tardar varios minutos en crearse. Para supervisar el proceso de creación del servicio, ejecute el siguiente comando. Cuando el valor *EXTERNAL-IP* del servicio *azure-vote-front* cambia de *pending* a una dirección IP, la aplicación está lista y se puede acceder a ella en la dirección IP externa.

```bash
kubectl get service -w
```

Salida:

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

Cuando el servicio esté listo, ejecute `CTRL-C` para terminar la inspección de kubectl.

Navegue hasta la dirección IP externa devuelta para ver la aplicación.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha implementado la aplicación Azure Vote en un clúster de Kubernetes de Azure Container Service. Las tareas completadas incluyen:  

> [!div class="checklist"]
> * Introducción a los objetos de Kubernetes
> * Descargar archivos de manifiesto de Kubernetes
> * Ejecución de una aplicación en Kubernetes
> * Prueba de la aplicación

Pase al siguiente tutorial para obtener información acerca de cómo escalar una aplicación Kubernetes y la infraestructura de Kubernetes subyacente. 

> [!div class="nextstepaction"]
> [Escalar infraestructura y aplicación de Kubernetes](./container-service-tutorial-kubernetes-scale.md)
