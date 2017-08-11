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
ms.date: 07/25/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Ejecución de aplicaciones en Kubernetes

En este tutorial, la cuarta parte de siete, se implementa una aplicación de ejemplo en un clúster de Kubernetes. Los pasos completados incluyen:

> [!div class="checklist"]
> * Descargar archivos de manifiesto de Kubernetes
> * Ejecución de una aplicación en Kubernetes
> * Prueba de la aplicación

En tutoriales posteriores se escala horizontalmente y se actualiza esta aplicación, y se configura Operations Management Suite para supervisar el clúster de Kubernetes.

En este tutorial se supone que tiene unos conocimientos básicos de los conceptos de Kubernetes. Para obtener una información más detallada sobre esta aplicación, consulte la [documentación de Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se empaquetó una aplicación en una imagen de contenedor, se cargó esta imagen en Azure Container Registry y se creó un clúster de Kubernetes. Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service). 

Como mínimo, este tutorial requiere un clúster de Kubernetes.

## <a name="get-manifest-file"></a>Obtención del archivo de manifiesto

En este tutorial, los [objetos de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) se implementan mediante un manifiesto de Kubernetes. Un manifiesto de Kubernetes es un archivo con formato de YAML o JSON que contiene instrucciones de implementación y configuración del objeto de Kubernetes.

El archivo de manifiesto de aplicación de este tutorial está disponibles en el repositorio de la aplicación Azure Vote, que se clonó en un tutorial anterior. Si aún no lo ha hecho, clone el repositorio con el siguiente comando: 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

El archivo de manifiesto se encuentra en el siguiente directorio del repositorio clonado.

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## <a name="update-manifest-file"></a>Actualización del archivo de manifiesto

Si usa Azure Container Registry para almacenar las imágenes de contenedor, el manifiesto debe actualizarse con el nombre loginServer de ACR.

El nombre del servidor de inicio de sesión de ACR se obtiene con el comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

El manifiesto de ejemplo se creó previamente con el nombre de repositorio de *microsoft*. Abra el archivo con cualquier editor de texto y reemplace el valor *microsoft* con el nombre del servidor de inicio de sesión de la instancia de ACR.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>Implementación de la aplicación

Use el comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para ejecutar la aplicación. Este comando analiza el archivo de manifiesto y crea los objetos de Kubernetes definidos.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
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

Para supervisar el progreso, utilice el comando [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) con el argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

En un primer momento, el parámetro **EXTERNAL-IP** del servicio *azure-vote-front* aparece como *pendiente*. Una vez que el parámetro EXTERNAL-IP ha cambiado de *pendiente* a una *dirección IP*, use `CTRL-C` para detener el proceso de inspección de kubectl.

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
