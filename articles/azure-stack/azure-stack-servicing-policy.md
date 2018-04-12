---
title: Directiva de mantenimiento de Azure Stack | Microsoft Docs
description: Aprenda sobre la directiva de servicio de Azure Stack y cómo mantener un sistema integrado en un estado admitido.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: mabrigg
ms.openlocfilehash: e37b63580d8cea4b5772bc54f7b2f79980afc0bc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="azure-stack-servicing-policy"></a>Directiva de mantenimiento de Azure Stack
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
Para continuar recibiendo soporte técnico, debe mantener actualizada la implementación de Azure Stack. La directiva para el aplazamiento de actualizaciones dice que para que Azure Stack permanezca dentro del soporte técnico, debe ejecutar la versión de actualización más reciente o ejecutar cualquiera de las dos versiones de actualización importante anteriores.  Las revisiones no se consideran versiones de actualización importantes.  Si a la nube de Azure Stack se queda atrás en *más de dos actualizaciones*, se considera fuera de cumplimiento y se debe actualizar con, al menos, la versión mínima admitida para recibir soporte técnico. 

Por ejemplo, si la versión de actualización disponible más reciente es la 1805 y las dos actualizaciones anteriores eran las versiones 1804 y 1803, ambas permanecen dentro del soporte técnico. Sin embargo, la 1802 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1805 y no había ninguna versión 1804, las dos actualizaciones anteriores de 1803 y 1802 permanecerían dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la actualización anterior como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente. 


## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)


