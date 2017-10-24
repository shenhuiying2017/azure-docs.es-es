---
title: "P+F sobre los problemas de implementación con Microsoft Azure Cloud Services | Microsoft Docs"
description: "En este artículo se enumeran las preguntas frecuentes sobre la implementación con Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/20/2017
ms.author: genli
ms.openlocfilehash: 755b8e7414f6e77d0013d2678e8d4228091e1e4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de implementación con Azure Cloud Services: preguntas más frecuentes (P+F)

En este artículo se incluyen preguntas frecuentes sobre problemas de implementación con [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). También puede consultar la [página Tamaños de los servicios en la nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>¿Por qué a veces se produce un error de asignación de recursos al implementar un servicio en la nube en la ranura de ensayo si ya hay una implementación existente en la ranura de producción?
Si un servicio en la nube tiene una implementación en cualquier ranura, todo el servicio en la nube se ancla a un clúster concreto. Esto significa que si una implementación ya existe en la ranura de producción, una nueva implementación de ensayo solo se puede asignar en el mismo clúster que la ranura de producción.

La asignación produce un error cuando el clúster del servicio en la nube no tiene los recursos de proceso físicos suficientes para la solicitud de implementación.

Como ayuda para mitigar estos errores de asignación, consulte [Error de asignación de servicio en la nube: Soluciones](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>¿Por qué produce a veces un error de asignación el escalado vertical u horizontal de la implementación de un servicio en la nube?
Cuando se implementa un servicio en la nube, suele anclarse a un clúster concreto. Esto significa que el escalado vertical u horizontal de un servicio en la nube existente debe asignar instancias nuevas en el mismo clúster. Si el clúster está casi lleno o el tamaño o tipo de máquina virtual deseado no está disponible, se puede producir un error en la solicitud.

Como ayuda para mitigar estos errores de asignación, consulte [Error de asignación de servicio en la nube: Soluciones](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>¿Por qué implementar un servicio en la nube en un grupo de afinidad a veces produce un error de asignación?
Una nueva implementación en un servicio en la nube vacío se puede asignar mediante el tejido en cualquier clúster de esa región, a menos que el servicio en la nube esté anclado a un grupo de afinidad. Las implementaciones en el mismo grupo de afinidad se intentarán en el mismo clúster. Si la capacidad del clúster se está agotando, se puede producir un error en la solicitud.

Como ayuda para mitigar estos errores de asignación, consulte [Error de asignación de servicio en la nube: Soluciones](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>¿Por qué cambiar el tamaño de máquina virtual o agregar una nueva a un servicio en la nube existente produce a veces un error de asignación?
Los clústeres de un centro de datos pueden tener configuraciones de tipos de máquina diferentes (p. ej., serie A, serie Av2, serie D, serie Dv2, serie G, serie H, etc.). Pero no todos los clústeres tienen necesariamente todos los tipos de máquina virtual. Por ejemplo, si intenta agregar una máquina virtual serie D a un servicio en la nube que ya se ha implementado en un clúster de serie A únicamente, se producirá un error de asignación. Esto también sucede si se intenta cambiar los tamaños de SKU de máquina virtual (por ejemplo, de una serie A a una serie D).

Como ayuda para mitigar estos errores de asignación, consulte [Error de asignación de servicio en la nube: Soluciones](cloud-services-allocation-failures.md#solutions).

Para comprobar los tamaños disponibles en su región, consulte [Microsoft Azure: Productos disponibles por región](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>¿Por qué implementar un servicio en la nube en ocasiones produce errores por límites, cuotas o restricciones en la suscripción o el servicio?
La implementación de un servicio en la nube puede producir un error si los recursos que deben asignarse superan la cuota máxima o permitida o predeterminada para su servicio en el nivel de región/centro de datos. Para más información, consulte [Límites de Cloud Services](../azure-subscription-service-limits.md#cloud-services-limits).

También puede realizar el seguimiento del uso o la cuota actuales de la suscripción en el portal: Azure Portal = > Suscripciones = > \<suscripción correspondiente > = > "Usage + quota" (Uso y cuota).

También se puede recuperar la información de consumo y del uso de recursos a través de las API de facturación de Azure. Consulte [API de uso de recursos de Azure (versión preliminar)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>¿Cómo se cambia el tamaño de una máquina virtual de servicio en la nube sin volver a implementarlo?
No se puede cambiar el tamaño de una máquina virtual de servicio en la nube implementado sin volver a implementarlo. El tamaño de la máquina virtual está integrado en el CSDEF, que solo se actualiza con una nueva implementación.

Para más información, consulte [Actualización de un servicio en la nube](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>¿Por qué no puedo implementar Cloud Services mediante las API de Service Management o PowerShell cuando se usa la cuenta de Storage de Azure Resource Manager? 

Dado que el servicio en la nube es un recurso clásico que no es directamente compatible con el modelo de Azure Resource Manager, no es posible asociarlo con las cuentas de Storage de Azure Resource Manager. Estas son algunas opciones: 
 
- Implementación a través de la API de REST.

    Al realizar la implementación a través de la API de REST de Service Management, podría sortear la limitación mediante la especificación de una dirección URL de SAS a Blob Storage, que funcionará con la cuenta de Storage del modelo clásico y de Azure Resource Manager. Lea más acerca de la propiedad "PackageUrl" [aquí](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Implementación a través de [Azure Portal](https://portal.azure.com).

    Funcionará desde [Azure Portal](https://portal.azure.com) dado que la llamada pasa por un servidor proxy o shim que permite la comunicación entre recursos del modelo clásico y de Azure Resource Manager. 
 
