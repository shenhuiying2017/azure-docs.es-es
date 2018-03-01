---
title: Arquitectura del Kit de desarrollo de Microsoft Azure Stack | Microsoft Docs
description: Vea la arquitectura del Kit de desarrollo de Microsoft Azure Stack.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b754ff5b5a82ac284eb59ff9b9d30a581f3d3af5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitectura del Kit de desarrollo de Microsoft Azure Stack

*Se aplica a: Kit de desarrollo de Azure Stack*

El Kit de desarrollo de Azure Stack es una implementación de un único nodo de Azure Stack. Todos los componentes se instalan en máquinas virtuales que se ejecutan en un solo equipo host. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitectura lógica
El siguiente diagrama muestra la arquitectura lógica del Kit de desarrollo de Azure Stack y sus componentes.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Roles de máquina virtual
El Kit de desarrollo de Azure Stack ofrece servicios a través de las siguientes máquinas virtuales en el host:

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


## <a name="next-steps"></a>pasos siguientes
[Implementación de Azure Stack](azure-stack-deploy.md)

[Primeros escenarios para probar](azure-stack-first-scenarios.md)

