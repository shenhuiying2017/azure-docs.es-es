---
title: "Ejemplo de script de la CLI de Azure: eliminación de una aplicación de un clúster"
description: "Ejemplo de script de la CLI de Azure: eliminación de una aplicación de un clúster de Service Fabric."
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Eliminación de una aplicación de un clúster de Service Fabric

Este script de ejemplo elimina una instancia de aplicación de Service Fabric en ejecución y anula el registro del tipo y la versión de una aplicación del clúster.  La eliminación de la instancia de la aplicación también elimina todas las instancias de servicio en ejecución asociadas a esa aplicación. A continuación, los archivos de aplicación se eliminan del almacén de imágenes. 

Si es necesario, instale la [CLI de Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script de ejemplo

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Quitar una aplicación de un clúster")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Selecciona el clúster con el que trabajar. |
| [sf application delete](/cli/azure/sf/application#delete) | Elimina la instancia de la aplicación del clúster. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Anula el registro del tipo y la versión de una aplicación de Service Fabric del clúster.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Quita un paquete de aplicación de Service Fabric del almacén de imágenes. |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Azure](../service-fabric-azure-cli-2-0.md).

Puede encontrar ejemplos de la CLI de Azure para Azure Service Fabric en los [ejemplos de la CLI de Azure](../samples-cli.md).

