---
title: "Modelos de conexión de sistemas integrados en Azure Stack | Microsoft Docs"
description: "Determinación de las decisiones de planeamiento de implementación para Azure Stack en varios nodos."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 0151b64b39699e2a7a804cdc57e368fd786119a7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modelos de conexión de sistemas integrados en Azure Stack
Si está interesado en un sistema integrado de Azure Stack, será preciso que comprenda [varias consideraciones de integración del centro de datos](azure-stack-datacenter-integration.md) para la implementación de Azure Stack y determinar cómo va a ajustarse el sistema a su centro de datos. Además, tendrá que decidir exactamente de qué forma va a integrar Azure Stack en su entorno de nube híbrida. En este artículo se proporciona información general acerca de estas decisiones importantes, lo que incluye decisiones acerca de la conexión de Azure, del almacén de identidades y del modelo de facturación.

Si decide comprar un sistema integrado, el proveedor de hardware del fabricante de equipo original (OEM) le guiará a través de gran parte del proceso de planeamiento con más detalle. Asimismo, le ayudará a realizar la implementación real.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Elija un modelo de conexión de la implementación de Azure Stack
Puede implementar Azure Stack, ya sea conectado a Internet (y a Azure) o desconectado. Para obtener el máximo provecho de Azure Stack, incluidos los escenarios híbridos entre Azure Stack y Azure, querrá implementar conectado a Azure. Esto define las opciones que están disponibles tanto para el almacén de identidades (Azure Active Directory o Servicios de federación de Active Directory) como para el modelo de facturación (facturación basada en pago por uso o facturación por capacidad), como se resume en el diagrama y la tabla siguientes: 

![Escenarios de facturación e implementación de Azure Stack](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Este es un punto clave en las decisiones. La elección de Servicios de federación de Active Directory (AD FS) o de Azure Active Directory (Azure AD) es una decisión que se toma una sola vez y se realiza en el momento de la implementación. Para cambiarlo posteriormente es preciso volver a implementar todo el sistema.  


|Opciones|Con conexión a Azure|Sin conexión a Azure|
|-----|-----|-----|
|Azure AD|![Compatible](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Compatible](media/azure-stack-deployment-decisions/check.png)|![Compatible](media/azure-stack-deployment-decisions/check.png)|
|Facturación por consumo|![Compatible](media/azure-stack-deployment-decisions/check.png)| |
|Facturación por capacidad|![Compatible](media/azure-stack-deployment-decisions/check.png)|![Compatible](media/azure-stack-deployment-decisions/check.png)|
|Descargar los paquetes de actualización directamente de Azure Stack|![Compatible](media/azure-stack-deployment-decisions/check.png)|  |

Una vez que haya decidido el modelo de conexión de Azure que va a usar para la implementación de Azure Stack, deben tomarse más decisiones que dependen de la conexión para el método de facturación y el almacén de identidades. 

## <a name="next-steps"></a>pasos siguientes

[Decisiones de implementación de Azure Stack con conexión a Azure](azure-stack-connected-deployment.md)

[Decisiones de implementación de Azure Stack sin conexión a Azure](azure-stack-disconnected-deployment.md)
