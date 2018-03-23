---
title: Obtención de registros de kubelet desde Azure Container Service (AKS)
description: Obtención de registros de kubelet desde nodos de clúster de Azure Container Service (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 56e20a9f9d17eac01e6f85007db41dcc417f83e4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="get-kubelet-logs-from-azure-container-service-aks-cluster-nodes"></a>Obtención de registros de kubelet desde nodos de clúster de Azure Container Service (AKS)

En ocasiones, necesitará obtener registros de kubelet desde un nodo de Azure Container Service (AKS) para solucionar problemas. Este documento detalla una opción para extraer estos registros.

## <a name="create-an-ssh-connection"></a>Creación de una conexión SSH

En primer lugar, cree una conexión SSH con el nodo en el que necesita extraer los registros de kubelet. Esta operación se describe en el documento [Conexión SSH con los nodos de un clúster de Azure Container Service (AKS)][aks-ssh].

## <a name="get-kubelet-logs"></a>Obtención de registros de kubelet

Una vez que se ha conectado al nodo, ejecute el siguiente comando para extraer los registros kubelet.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Salida de ejemplo:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md