---
title: "Ejemplo de script de la CLI de Service Fabric: Actualización de una aplicación en un clúster"
description: "Ejemplo de script de la CLI de Service Fabric: Actualización de una aplicación con una nueva versión Este ejemplo también actualiza una aplicación implementada con los nuevos bits."
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
ms.openlocfilehash: 059022a7f447fe2b0decaacf44ef7bc2e9a241d1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Incorporación de un certificado de aplicación a un clúster de Service Fabric

Este script de ejemplo carga una nueva versión de una aplicación existente y, después, actualiza una aplicación implementada con los nuevos bits.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [documentación de la CLI de Service Fabric](../service-fabric-cli.md).

Puede encontrar ejemplos adicionales de la CLI de Service Fabric para Azure Service Fabric en los [ejemplos de la CLI de Service Fabric](../samples-cli.md).
