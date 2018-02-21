---
title: "Actualización de un clúster de Azure Container Service (AKS)"
description: "Actualización de un clúster de Azure Container Service (AKS)"
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 6eaa0128c37d74fd2fd4c4bdb377ca76d7c37669
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Actualización de un clúster de Azure Container Service (AKS)

Azure Container Service (AKS) facilita las tareas de administración comunes, incluida la actualización de clústeres de Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Actualización de un clúster de AKS

Antes de actualizar un clúster, use el comando `az aks get-versions` para comprobar que si versiones de Kubernetes están disponibles para la actualización.

```azurecli-interactive
az aks get-versions --name myAKSCluster --resource-group myResourceGroup --output table
```

Salida:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

Tenemos tres versiones disponibles para la actualización: 1.7.9, 1.8.1 y 1.8.2. El comando `az aks upgrade` se puede usar para actualizar a la última versión disponible.  Durante el proceso de actualización, los nodos se [acordonan y vacían][kubernetes-drain] minuciosamente para minimizar las interrupciones en las aplicaciones en ejecución.  Antes de iniciar una actualización de clúster, asegúrese de que dispone de suficiente capacidad de proceso adicional para manipular la carga de trabajo a medida que se agregan y eliminan los nodos de clúster.

```azurecli-interactive
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

Puede comprobar que la actualización se realizó correctamente con el comando `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Salida:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>pasos siguientes

Obtenga más información sobre la implementación y administración de AKS con los tutoriales de AKS.

> [!div class="nextstepaction"]
> [Tutorial de AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md