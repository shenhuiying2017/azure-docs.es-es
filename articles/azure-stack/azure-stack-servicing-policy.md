---
title: Directiva de mantenimiento de Azure Stack | Microsoft Docs
description: Aprenda sobre la directiva de servicio de Azure Stack y cómo mantener un sistema integrado en un estado admitido.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 77cc2f80588a104880e8149daccc6debd1ec43bc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="azure-stack-servicing-policy"></a>Directiva de mantenimiento de Azure Stack
En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido. 

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay dos tipos de actualizaciones para los sistemas integrados: 

- **Actualizaciones de software de Microsoft**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estas actualizaciones pueden incluir las últimas actualizaciones de seguridad de Windows Server, las actualizaciones que no son de seguridad y las actualizaciones de características de Azure Stack. Puede descargar estas actualizaciones directamente de Microsoft.

- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.


## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones
Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad. 

Busque documentación sobre cómo planear y administrar actualizaciones y cómo determinar la versión actual en [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md). Para obtener información sobre una actualización concreta, incluido cómo descargarla, vea las notas de versión de la actualización: 
- [Actualización de Azure Stack 1803](azure-stack-update-1803.md)
- [Actualización de Azure Stack 1802](azure-stack-update-1802.md)
- [Actualización de Azure Stack 1712](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Revisiones
En ocasiones, Microsoft ofrece revisiones para Azure Stack que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente.  Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución. 

Las revisiones se descargan e instalan del mismo modo que las actualizaciones completas normales de Azure Stack. Pero, a diferencia de una actualización completa, las revisiones pueden instalarse en minutos. Se recomienda que los operadores de Azure Stack establezcan períodos de mantenimiento al instalar las revisiones. Las revisiones actualizan la versión de la nube de Azure Stack para se que pueda determinar fácilmente si la revisión se ha aplicado. Se ofrece una revisión independiente para cada versión de Azure Stack siga dentro del soporte técnico. Cada revisión para una iteración concreta es acumulativa e incluye las actualizaciones anteriores de esa misma versión. Puede obtener más información sobre la aplicabilidad de una revisión determinada en el correspondiente artículo de revisiones de Knowledge Base.  


## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico
Para continuar recibiendo soporte técnico, debe mantener actualizada la implementación de Azure Stack. La directiva de aplazamiento de actualizaciones dice que, para que Azure Stack permanezca dentro del soporte técnico, debe ejecutar la versión de actualización más reciente o una de las dos versiones de actualización anteriores. Las revisiones no se consideran versiones de actualización importantes. Si a la nube de Azure Stack se retrasa en *más de dos actualizaciones*, se considera fuera de cumplimiento y debe actualizarse con, al menos, la versión mínima admitida para recibir soporte técnico. 

Por ejemplo, si la versión de actualización disponible más reciente es la 1805 y las dos actualizaciones anteriores eran las versiones 1804 y 1803, ambas permanecen dentro del soporte técnico. Sin embargo, la 1802 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1805 y no hubo ninguna versión 1804, las dos actualizaciones anteriores de 1803 y 1802 permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la actualización anterior como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente. 

## <a name="get-support"></a>Obtención de soporte técnico
Azure Stack sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de servicios en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico.  Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)


