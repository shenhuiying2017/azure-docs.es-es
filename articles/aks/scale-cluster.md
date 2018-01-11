---
title: "Escala de un clúster de Azure Container Service (AKS)"
description: "Escale un clúster de Azure Container Service (AKS)."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: a5380a3815335d7347b57dac49a3dca02c9d981c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Escala de un clúster de Azure Container Service (AKS)

Es fácil escalar un clúster de AKS a un número de nodos diferente.  Seleccione el número de nodos deseado y ejecute el comando `az aks scale`.  Durante la reducción vertical, los nodos se [acordonarán y vaciarán][kubernetes-drain] minuciosamente para minimizar las interrupciones en las aplicaciones en ejecución.  Durante el escalado vertical, el comando `az` espera hasta que el clúster de Kubernetes marca los nodos como `Ready`.

## <a name="scale-the-cluster-nodes"></a>Escalado de los nodos de clúster

Use el comando `az aks scale` para escalar los nodos de clúster. En el siguiente ejemplo se escala un clúster denominado *myK8SCluster* en un único nodo.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la implementación y administración de AKS con los tutoriales de AKS.

> [!div class="nextstepaction"]
> [Tutorial de AKS][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md