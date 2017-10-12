---
title: Directiva de mantenimiento de Azure Stack | Microsoft Docs
description: "Aprenda sobre la directiva de servicio de Azure Stack y cómo mantener un sistema integrado en un estado admitido."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f2c99f19b30d2cdfdf65dea6dd3909b88ffc15dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Directiva de mantenimiento de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido. 

## <a name="update-package-types"></a>Tipos de actualizaciones

Existen dos tipos de actualizaciones para sistemas integrados; actualizaciones de software de Microsoft y actualizaciones que son específicas del proveedor de hardware OEM (fabricante de equipo original), como controladores y firmware. Estas actualizaciones se proporcionan como actualizaciones de Azure Stack independientes y se administran de manera independiente.

- **Actualizaciones de software de Microsoft**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estas actualizaciones pueden incluir las últimas actualizaciones de seguridad de Windows Server, las actualizaciones que no son de seguridad y las actualizaciones de características de Azure Stack. Puede descargar estas actualizaciones directamente de Microsoft.
- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones

Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Una actualización de Microsoft tiene la siguiente convención de nomenclatura para ayudarle a identificar fácilmente la fecha de lanzamiento:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Por ejemplo, una actualización de software de Microsoft lanzada el 15 de junio de 2017 tendrá la versión "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

Para recibir soporte técnico para su sistema, debe mantener actualizado Azure Stack dentro de un intervalo de tiempo específico. Nuestra directiva de aplazamiento de las actualizaciones de software de Microsoft es de tres meses. Si el sistema lleva sin actualizarse más de tres meses, si considerará que no es compatible. Para recibir soporte técnico, deberá actualizar a al menos la versión mínima admitida. 

Las actualizaciones de Microsoft no son acumulativas y necesitan la actualización anterior como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

En la tabla siguiente se muestran las versiones de actualización, su requisito previo y la versión mínima admitida que debe tener el sistema para mantener la compatibilidad. La tabla se basa en la versión inicial de los sistemas integrados de Azure Stack (compilación 1708), con la primera versión de actualización (1709) de septiembre de 2017. 

| Última actualización (*ejemplo*) | Requisito previo | Versión mínima admitida |
| -- | -- | -- |
| 1709 | Compilación 1708 | N/D |
| 1710 | 1709 | N/D |
| 1711 | 1710 | N/D |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)


