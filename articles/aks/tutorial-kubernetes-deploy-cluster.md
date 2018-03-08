---
title: "Tutorial de Kubernetes en Azure: implementación del clúster"
description: "Tutorial de AKS: implementación del clúster"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 975069dbe9283c98482d7d0d5741a595ef323b35
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Implementación de un clúster de Azure Container Service (AKS)

Kubernetes proporciona una plataforma distribuida para aplicaciones en contenedores. Con AKS, el aprovisionamiento de un clúster de Kubernetes listo para producción se realiza de forma rápida y sencilla. En este tutorial, la tercera parte de ocho, se implementa un clúster de Kubernetes en AKS. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de un clúster de AKS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

En tutoriales posteriores, la aplicación Azure Vote se implementa en el clúster, se escala y se actualiza, y Operations Management Suite se configura para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores, se creó una imagen de contenedor y se actualizó en una instancia de Azure Container Registry. Si no ha realizado estos pasos, pero desea continuar, vuelva al [tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].

## <a name="enable-aks-preview"></a>Habilitación de la versión preliminar de AKS

Mientras AKS está en versión preliminar, la creación de nuevos clústeres requiere una marca de característica en su suscripción. Esta característica la puede solicitar para cualquier número de suscripciones que desee utilizar. Use el comando `az provider register` para registrar el proveedor de AKS:

```azurecli
az provider register -n Microsoft.ContainerService
```

Después del registro, ya está listo para crear un clúster de Kubernetes con AKS.

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes

En el siguiente ejemplo se crea un clúster denominado `myAKSCluster` en un grupo de recursos llamado `myResourceGroup`. Este grupo de recursos se creó en el [tutorial anterior][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Después de varios minutos, la implementación se completa y devuelve información en formato json sobre la implementación de AKS.

## <a name="install-the-kubectl-cli"></a>Instalación de la CLI de kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [kubectl][kubectl], el cliente de la línea de comandos de Kubernetes.

Si usa Azure CloudShell, kubectl ya está instalado. Si desea instalarlo en un entorno local, ejecute el siguiente comando:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Conexión con kubectl

Para configurar kubectl para la conexión al clúster de Kubernetes, ejecute el comando siguiente:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

Para comprobar la conexión al clúster, ejecute el comando [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Salida:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

Al finalizar el tutorial, tendrá un clúster de AKS preparado para cargas de trabajo. En los tutoriales posteriores, una aplicación de contenedores múltiples está implementada en este clúster, escalada horizontalmente, actualizada y supervisada.

## <a name="configure-acr-authentication"></a>Configuración de la autenticación de ACR

La autenticación debe configurarse entre el clúster de AKS y el registro de ACR. Esto implica conceder a la identidad de ACS los derechos adecuados para extraer imágenes del registro de ACR.

En primer lugar, obtenga el identificador de la entidad de servicio configurado para AKS. Actualice el nombre del grupo de recursos y el nombre del clúster de AKS para que coincida con su entorno.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Obtenga el identificador del recurso del registro de ACR. Actualice el nombre de registro con el del registro de ACR y el grupo de recursos con el grupo de recursos donde se encuentra el registro de ACR.

```azurecli
ACR_ID=$(az acr show --name myACRRegistry --resource-group myResourceGroup --query "id" --output tsv)
```

Cree la asignación de roles, que concede el acceso adecuado.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se implementó un clúster de Kubernetes en AKS. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de un clúster de AKS de Kubernetes
> * Instalación de la CLI de Kubernetes (kubectl)
> * Configuración de kubectl

Avance al siguiente tutorial para aprender a ejecutar la aplicación en el clúster.

> [!div class="nextstepaction"]
> [Implementación de una aplicación en Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md