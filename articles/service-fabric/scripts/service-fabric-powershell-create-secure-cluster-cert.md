---
title: "Ejemplo de script de Azure PowerShell: creación de un clúster de Service Fabric | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: creación de un clúster de Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: dc0412b7595e9a84049223561c7695a86480fe4a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2017
---
# <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric

En este script de ejemplo se crea un clúster de Service Fabric de cinco nodos protegido con un certificado X.509.  El comando crea un certificado autofirmado y lo carga en un nuevo almacén de claves. El certificado también se copia en un directorio local.  Establezca el parámetro *-OS* para elegir la versión de Windows o Linux que se ejecuta en los nodos del clúster.  Personalice los parámetros según sea necesario.

Si es necesario, instale Azure PowerShell con la instrucción que se encuentra en la [guía de Azure PowerShell](/powershell/azure/overview) y luego ejecute `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, el clúster y todos los recursos relacionados.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Crea un clúster de Azure Service Fabric. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de Azure PowerShell para Azure Service Fabric en los [ejemplos de PowerShell](../service-fabric-powershell-samples.md).
