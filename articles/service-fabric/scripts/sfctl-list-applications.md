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
ms.date: 09/19/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: 8e8a3e4b1f63cf220e360c1351c7bccc3dab96ee
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Mostrar las aplicaciones que se ejecutan en un clúster de Service Fabric

Este script de ejemplo se conecta a un clúster de Service Fabric y enumera todas las aplicaciones aprovisionadas.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Service Fabric](../service-fabric-cli.md).

Puede encontrar ejemplos adicionales de la CLI de Service Fabric para Azure Service Fabric en los [ejemplos de la CLI de Service Fabric](../samples-cli.md).
