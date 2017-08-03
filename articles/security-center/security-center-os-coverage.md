---
title: Plataformas compatibles con Azure Security Center | Microsoft Docs
description: En este documento se proporciona una lista de sistemas operativos Windows y Linux compatibles con Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c33e132037d95fa92fd59a8243a9a8a351ae0224
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="supported-platforms-in-azure-security-center"></a>Plataformas compatibles con Azure Security Center
La supervisión del estado de seguridad y las recomendaciones están disponibles para las máquinas virtuales creadas con los modelos de implementación clásica y de Resource Manager.

> [!NOTE]
> Obtenga más información sobre los [modelos de implementación clásico y de Resource Manager](../azure-classic-rm.md) para los recursos de Azure.
>
>

## <a name="supported-platforms-for-windows-vms"></a>Plataformas compatibles con máquinas virtuales Windows
Sistemas operativos Windows compatibles:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

> [!NOTE]
>
* Las evaluaciones de vulnerabilidad del sistema operativo todavía no están disponibles para Windows Server 2016.
* Las detecciones de análisis de bloqueos solo son compatibles con Windows Server 2012 y Windows Server 2012 R2.
>
>

## <a name="supported-platforms-for-linux-vms"></a>Plataformas compatibles con máquinas virtuales Linux
Sistemas operativos Linux compatibles:

* Versiones de Ubuntu 12.04, 14.04 y 16.04 y 16.10
* Versiones de Debian 7, 8
* Versiones de CentOS 6.\*, 7.*
* Versiones de Red Hat Enterprise Linux (RHEL) 6.\*, 7.*
* Versiones de SUSE Linux Enterprise Server (SLES) 11 SP4+, 12.*
* Versiones de Oracle Linux 6.\*, 7.*

> [!NOTE]
> El análisis de comportamiento de máquinas virtuales todavía no está disponible para los sistemas operativos Linux.
>
>

## <a name="vms-and-cloud-services"></a>Servicios en la nube y máquinas virtuales
También se admiten máquinas virtuales que se ejecuten en un servicio en la nube. Se supervisan los roles de web y de trabajo de servicios en la nube que se ejecutan en espacios de producción. Para aprender más sobre el servicio en la nube, consulte la [información general sobre los servicios en la nube](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Pasos siguientes

- [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a realizar planes y conozca las consideraciones de diseño necesarias para adoptar Azure Security Center.
- [Alertas de seguridad por tipo de Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis): obtenga más información sobre el análisis de comportamiento de máquinas virtuales y el análisis de la memoria de volcado en Security Center.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre cumplimiento y seguridad de Azure.

