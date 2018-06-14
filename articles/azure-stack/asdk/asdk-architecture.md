---
title: Arquitectura del Kit de desarrollo de Azure Stack | Microsoft Docs
description: Describe la arquitectura del Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
ms.locfileid: "29974141"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitectura del Kit de desarrollo de Microsoft Azure Stack
El Kit de desarrollo de Azure Stack (ASDK) es una implementación de un único nodo de Azure Stack. Todos los componentes se instalan en máquinas virtuales que se ejecutan en un solo equipo host. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitectura lógica
El siguiente diagrama muestra la arquitectura lógica del Kit de desarrollo de Azure Stack y sus componentes.

![Arquitectura de ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Roles de máquina virtual
El Kit de desarrollo de Azure Stack ofrece servicios a través de las siguientes máquinas virtuales hospedadas en el equipo host del kit de desarrollo:

| NOMBRE | DESCRIPCIÓN |
| ----- | ----- |
| **AzS-ACS01** | Servicios de almacenamiento de Azure Stack.|
| **AzS-ADFS01** | Servicios de federación de Active Directory (AD FS).  |
| **AzS-BGPNAT01** | Enrutador perimetral que proporciona funcionalidades de NAT y VPN para Azure Stack. |
| **AzS-CA01** | Servicios de entidad de certificación para servicios de rol de Azure Stack.|
| **AzS-DC01** | Servicios de Active Directory, DNS y DHCP para Microsoft Azure Stack.|
| **AzS-ERCS01** | Máquina virtual de la consola de recuperación de emergencia. |
| **AzS-GWY01** | Servicios perimetrales de puerta de enlace, como las conexiones de sitio a sitio de VPN para redes de inquilinos.|
| **AzS-NC01** | Controladora de red, que administra los servicios de red de Azure Stack.  |
| **AzS-SLB01** | Servicios de multiplexor del equilibrio de carga en Azure Stack para los inquilinos y los servicios de infraestructura de Azure Stack.  |
| **AzS-SQL01** | Almacén de datos internos para los roles de infraestructura de Azure Stack.  |
| **AzS-WAS01** | Portal de administración de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-WASP01**| Portal del usuario (inquilino) de Azure Stack y servicios de Azure Resource Manager.|
| **AzS-XRP01** | Controlador de administración de infraestructura para Microsoft Azure Stack, incluidos los proveedores de recursos de Compute, Networks y Storage.|


## <a name="next-steps"></a>Pasos siguientes
[Información acerca de las tareas de administración básicas de ASDK](asdk-admin-basics.md)
