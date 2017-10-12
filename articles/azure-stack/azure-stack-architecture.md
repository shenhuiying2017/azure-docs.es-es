---
title: Arquitectura de Microsoft Azure Stack Development Kit | Microsoft Docs
description: Vea la arquitectura de Microsoft Azure Stack Development Kit.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitectura de Microsoft Azure Stack Development Kit

*Se aplica a: Azure Stack Development Kit*

Azure Stack Development Kit es una implementación de un único nodo de Azure Stack. Todos los componentes se instalan en máquinas virtuales que se ejecutan en un solo equipo host. 

## <a name="logical-architecture-diagram"></a>Diagrama de arquitectura lógica
El siguiente diagrama muestra la arquitectura lógica de Azure Stack Development Kit y sus componentes.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Roles de máquina virtual
Azure Stack Development Kit ofrece servicios a través de las siguientes máquinas virtuales en el host:

| Nombre | Descripción |
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
[Implementación de Azure Stack](azure-stack-deploy.md)

[Primeros escenarios para probar](azure-stack-first-scenarios.md)

