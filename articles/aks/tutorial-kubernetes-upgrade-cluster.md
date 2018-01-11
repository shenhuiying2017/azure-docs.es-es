---
title: "Tutorial de Kubernetes en Azure: actualización del clúster"
description: "Tutorial de Kubernetes en Azure: actualización del clúster"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5fd9a1890c1940cdd4e79cc32e0b3984edd043e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Actualización de Kubernetes en Azure Container Service (AKS)

Un clúster de Azure Container Service (AKS) se puede actualizar mediante la CLI de Azure. Durante el proceso de actualización, los nodos de Kubernetes se [acordonan y vacían][kubernetes-drain] minuciosamente para minimizar las interrupciones en las aplicaciones en ejecución.

En este tutorial, la octava parte de ocho, se actualiza un clúster de Kubernetes. Las tareas que debe completar incluyen las siguientes:

> [!div class="checklist"]
> * Identificación de las versiones de Kubernetes actuales y disponibles
> * Actualización de los nodos de Kubernetes
> * Validación de una actualización correcta

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, se ha cargado esta imagen en Azure Container Registry y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes.

Si no ha realizado estos pasos y quiere continuar, vuelva al [tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Obtención de versiones del clúster

Antes de actualizar un clúster, use el comando `az aks get-versions` para comprobar que si versiones de Kubernetes están disponibles para la actualización.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Aquí puede ver que la versión del nodo actual es `1.7.7` y que las versiones `1.7.9`, `1.8.1` y `1.8.2` están disponibles.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Actualización del clúster

Use el comando `az aks upgrade` para actualizar los nodos de clúster. En los ejemplos siguientes se actualiza el clúster a la versión `1.8.2`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Salida:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
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

Puede comprobar que la actualización se realizó correctamente con el comando `az aks show`.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Salida:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
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