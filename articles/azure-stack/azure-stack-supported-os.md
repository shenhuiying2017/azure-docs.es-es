---
title: Sistemas operativos invitados compatibles con Azure Stack | Microsoft Docs
description: Estos sistemas operativos invitados pueden usarse en Azure Stack.
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: c9f5bee38772623fb79fa081be8eaece981cc8ab
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas operativos invitados compatibles con Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

## <a name="windows"></a>Windows
Azure Stack admite los siguientes sistemas operativos invitados Windows. En Marketplace hay imágenes disponibles para descargarlas en Azure Stack. Las imágenes del cliente Windows no están disponibles en Marketplace.

Durante la implementación, Azure Stack se asegurará de que se aplica una versión adecuada del agente invitado en la imagen.

| Sistema operativo | DESCRIPCIÓN | Publicador | OS Type (Tipo de SO) | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bits | Microsoft | Windows | Centro de datos |
| Windows Server 2012 | 64 bits | Microsoft | Windows | Centro de datos |
| Windows Server 2012 R2 | 64 bits | Microsoft | Windows | Centro de datos |
| Windows Server 2016 | 64 bits | Microsoft | Windows | Datacenter, Datacenter Core, Datacenter con Containers |
| Windows 7 | 64 bits, Pro y Enterprise | Microsoft | Windows | Sin  |
| Windows 8.1 | 64 bits, Pro y Enterprise | Microsoft | Windows | Sin  |
| Windows 10 *(vea la nota 1)* | 64 bits, Pro y Enterprise | Microsoft | Windows | Sin  |

***Nota 1:***  *Para implementar sistemas operativos cliente Windows 10 en Azure Stack, debe tener una [licencia por usuario de Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) o adquirirlo a través de un proveedor de servicios de hosting cualificado multiinquilino ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*


## <a name="linux"></a>Linux

Las distribuciones Linux que se indican aquí incluyen el agente Linux de Microsoft Azure (WALA).

> [!NOTE]   
> Las imágenes creadas con versiones de WALA posteriores a la 2.2.3 *no* son compatibles y es poco probable que se puedan implementar. Se sabe que algunas versiones de agente WALA no funcionan en máquinas virtuales de Azure Stack, incluidas las versiones 2.2.12 y 2.2.13.


| Distribución | DESCRIPCIÓN | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Container Linux |  64 bits | CoreOS | Stable |
| Basado en CentOS 6.9 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.3 | 64 bits | Rogue Wave | Sí |
| Basado en CentOS 7.4 | 64 bits | Rogue Wave | Sí |
| Debian 8 "Jessie" | 64 bits | credativ |  Sí |
| Debian 9 "Stretch" | 64 bits | credativ | Sí |
| Oracle Linux | 64 bits | Oracle | Sin  |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat | Sin  |
| SLES 11SP4 | 64 bits | SUSE | Sí |
| SLES 12SP3 | 64 bits | SUSE | Sí |
| Ubuntu 14.04 LTS | 64 bits | Canonical | Sí |
| Ubuntu 16.04 LTS | 64 bits | Canonical | Sí |

Puede que otras distribuciones Linux sean compatibles en el futuro.
