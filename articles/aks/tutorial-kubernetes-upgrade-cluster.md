---
title: 'Tutorial de Kubernetes en Azure: actualización del clúster'
description: 'Tutorial de Kubernetes en Azure: actualización del clúster'
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0886d13b62b6b8ad1c0dcd430ce48bcc51d6d465
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933838"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Tutorial: Actualización de Kubernetes en Azure Kubernetes Service (AKS)

Un clúster de Azure Kubernetes Service (AKS) se puede actualizar mediante la CLI de Azure. Durante el proceso de actualización, los nodos de Kubernetes se [acordonan y vacían][kubernetes-drain] minuciosamente para minimizar las interrupciones en las aplicaciones en ejecución.

En este tutorial, la octava parte de ocho, se actualiza un clúster de Kubernetes. Las tareas que debe completar incluyen las siguientes:

> [!div class="checklist"]
> * Identificación de las versiones de Kubernetes actuales y disponibles
> * Actualización de los nodos de Kubernetes
> * Validación de una actualización correcta

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, se ha cargado esta imagen en Azure Container Registry y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes.

Si no ha realizado estos pasos y quiere continuar, vuelva al [tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Obtención de versiones del clúster

Antes de actualizar un clúster, use el comando `az aks get-upgrades` para comprobar que si versiones de Kubernetes están disponibles para la actualización.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

En este ejemplo, la versión del nodo actual es `1.7.9` y puede ver las versiones de actualizaciones disponibles en la columna correspondiente.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Actualización del clúster

Use el comando `az aks upgrade` para actualizar los nodos de clúster. En los ejemplos siguientes se actualiza el clúster a la versión `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Salida:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Validación de la actualización

Confirme que la actualización se realizó correctamente con el comando `az aks show`.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Salida:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, actualizó Kubernetes en un clúster de AKS. Se completaron las siguientes tareas:

> [!div class="checklist"]
> * Identificación de las versiones de Kubernetes actuales y disponibles
> * Actualización de los nodos de Kubernetes
> * Validación de una actualización correcta

Siga este vínculo para obtener más información acerca de AKS.

> [!div class="nextstepaction"]
> [Introducción a AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md