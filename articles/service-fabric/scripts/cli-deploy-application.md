---
title: "Ejemplo de script de la CLI de Azure: implementación de una aplicación en un clúster"
description: "Ejemplo de script de la CLI de Azure: implementación de una aplicación en un clúster de Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: es-es
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implementación de una aplicación en un clúster de Service Fabric

Este script de ejemplo copia un paquete de aplicación en un almacén de imágenes de clúster, registra el tipo de aplicación en el clúster y crea una instancia de la aplicación a partir del tipo de aplicación.  Si se definieron servicios predeterminados en el manifiesto de aplicación del tipo de aplicación de destino, también se crean en este momento.

Si es necesario, instale la [CLI de Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script de ejemplo

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Implementación de una aplicación en un clúster")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de que el ejemplo de script se haya ejecutado, el script de [Remove an application](cli-remove-application.md) (Quitar una aplicación) puede utilizarse para quitar la instancia de la aplicación, anular el registro del tipo de aplicación y eliminar el paquete de aplicación del almacén de imágenes.

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.


| Comando | Notas |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Selecciona el clúster con el que trabajar. |
| [sf application upload](/cli/azure/sf/application#upload) | Carga los archivos de aplicación y los manifiestos. |
| [sf application provision](/cli/azure/sf/application#provision) | Registra la aplicación en el clúster.|
| [sf application create](/cli/azure/sf/application#create) | Crea una instancia de la aplicación e implementa los servicios definidos en los nodos. |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Azure](../service-fabric-azure-cli-2-0.md).

Puede encontrar ejemplos de la CLI de Azure para Azure Service Fabric en los [ejemplos de la CLI de Azure](../samples-cli.md).

