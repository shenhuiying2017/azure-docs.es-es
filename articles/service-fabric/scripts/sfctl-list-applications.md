---
title: "Ejemplo de script CLI de Service Fabric: enumerar las aplicaciones de un clúster"
description: "Ejemplo de script CLI de Service Fabric: enumerar las aplicaciones aprovisionadas en un clúster de Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: f764c36bcc4905224242cbdf1602d7482e52efac
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Mostrar las aplicaciones que se ejecutan en un clúster de Service Fabric

Este script de ejemplo se conecta a un clúster de Service Fabric y enumera todas las aplicaciones aprovisionadas.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Service Fabric](../service-fabric-cli.md).

Puede encontrar ejemplos adicionales de la CLI de Service Fabric para Azure Service Fabric en los [ejemplos de la CLI de Service Fabric](../samples-cli.md).
