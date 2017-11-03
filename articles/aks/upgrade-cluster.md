---
title: "Actualización de un clúster de Azure Container Service (AKS) | Microsoft Docs"
description: "Actualización de un clúster de Azure Container Service (AKS)"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Kubernetes, Docker, Containers, microservicios, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 7b69f770354c9402a3248a1d5a27558ff9aea7c4
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Actualización de un clúster de Azure Container Service (AKS)

Azure Container Service (AKS) facilita las tareas de administración comunes, incluida la actualización de clústeres de Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Actualización de un clúster de AKS

Antes de actualizar un clúster, use el comando `az aks get-versions` para comprobar que si versiones de Kubernetes están disponibles para la actualización.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Salida:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades    AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  ----------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.1             1.7.7               1.8.1
```

Tenemos dos versiones disponibles para la actualización: 1.8.0 y 1.8.1.  El comando `az aks upgrade` se puede usar para actualizar a la última versión disponible.  Durante el proceso de actualización, los nodos se [acordonan y vacían](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) minuciosamente para minimizar las interrupciones en las aplicaciones en ejecución.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.1
```

Salida:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
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
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
    "kubernetesVersion": "1.8.1",
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
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Salida:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.1                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la implementación y administración de AKS con los tutoriales de AKS.

> [!div class="nextstepaction"]
> [Tutorial de AKS](./tutorial-kubernetes-prepare-app.md)