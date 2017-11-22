---
title: "Tutorial de Kubernetes en Azure: implementación del clúster | Microsoft Docs"
description: "Tutorial de AKS: implementación del clúster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 69dea4ab748d88d18cf01dc9b3fc1bdddd562681
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Implementación de un clúster de Azure Container Service (AKS)

Kubernetes proporciona una plataforma distribuida para aplicaciones en contenedores. Con AKS, el aprovisionamiento de un clúster de Kubernetes listo para producción se realiza de forma rápida y sencilla. En este tutorial, la tercera parte de ocho, se implementa un clúster de Kubernetes en AKS. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de un clúster de AKS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

En tutoriales posteriores, la aplicación Azure Vote se implementa en el clúster, se escala y se actualiza, y Operations Management Suite se configura para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores, se creó una imagen de contenedor y se actualizó en una instancia de Azure Container Registry. Si no ha realizado estos pasos, pero desea continuar, vuelva al tutorial [Create container images to be used with Azure Container Service](./tutorial-kubernetes-prepare-app.md) (Creación de las imágenes de contenedor que se usan con Azure Container Service).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Habilitación de la versión preliminar de AKS para su suscripción de Azure
Mientras AKS está en versión preliminar, la creación de nuevos clústeres requiere una marca de característica en su suscripción. Esta característica la puede solicitar para cualquier número de suscripciones que desee utilizar. Use el comando `az provider register` para registrar el proveedor de AKS:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Después del registro, ya está listo para crear un clúster de Kubernetes con AKS.

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes

En el siguiente ejemplo se crea un clúster denominado `myK8sCluster` en un grupo de recursos llamado `myResourceGroup`. Este grupo de recursos se creó en el [tutorial anterior](./tutorial-kubernetes-prepare-acr.md).

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Después de varios minutos, la implementación se completa y devuelve información en formato json sobre la implementación de AKS.

## <a name="install-the-kubectl-cli"></a>Instalación de la CLI de kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de la línea de comandos de Kubernetes.

Si usa Azure CloudShell, kubectl ya está instalado. Si desea instalarlo en un entorno local, ejecute el siguiente comando:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Conexión con kubectl

Para configurar kubectl para la conexión al clúster de Kubernetes, ejecute el comando siguiente:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para comprobar la conexión al clúster, ejecute el comando [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli
kubectl get nodes
```

Salida:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

Al finalizar el tutorial, tendrá un clúster de AKS preparado para cargas de trabajo. En los tutoriales posteriores, una aplicación de contenedores múltiples está implementada en este clúster, escalada horizontalmente, actualizada y supervisada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se implementó un clúster de Kubernetes en AKS. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de un clúster de AKS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

Avance al siguiente tutorial para aprender a ejecutar la aplicación en el clúster.

> [!div class="nextstepaction"]
> [Implementación de una aplicación en Kubernetes](./tutorial-kubernetes-deploy-application.md)
