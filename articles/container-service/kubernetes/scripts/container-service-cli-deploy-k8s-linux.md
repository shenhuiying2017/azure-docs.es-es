---
title: "Ejemplo de script de la CLI de Azure: creación de un clúster de ACS con Kubernetes para Linux | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de un clúster de ACS con Kubernetes para Linux"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: c6a392217f84f549f2cae3c68fed85b9f888db77
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---

# <a name="create-an-azure-container-service-kubernetes-linux-cluster"></a>Creación de un clúster de Azure Container Service con Kubernetes para Linux

En este ejemplo se crea un clúster de Azure Container Service que ejecuta Kubernetes para los contenedores basados en Linux.

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#create) | Crea un clúster de ACS. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Azure Container Service en la [documentación de Azure Container Service](../cli-samples.md).


