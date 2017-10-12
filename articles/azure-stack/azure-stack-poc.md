---
title: "¿Qué es Azure Stack? | Microsoft Docs"
description: Azure Stack le permite ejecutar servicios de Azure en su centro de datos.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 950ba44c0b7eb80c9b0a3c69a9fca03cd244576d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-stack"></a>¿Qué es Azure Stack?

Microsoft Azure Stack es una plataforma en la nube híbrida que le permite proporcionar servicios de Azure desde el centro de datos de la organización.  Azure Stack está diseñado para posibilitar nuevos escenarios para las aplicaciones modernas en escenarios clave, como los entornos perimetrales y los entornos desconectados, así como para resolver determinados requisitos de seguridad y de cumplimiento.  Azure Stack se ofrece en dos opciones de implementación para satisfacer sus necesidades.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados de Azure Stack
Los sistemas integrados de Azure Stack se suministran mediante una asociación de Microsoft y [de asociados de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), con lo que se crea una solución que ofrece innovación dirigida a la nube y equilibrada con simplicidad en la administración.  Dado que Azure Stack se suministra como un sistema integrado de hardware y software, se le ofrece la flexibilidad y el control adecuados, a la vez que puede seguir adoptando la innovación de la nube.  El tamaño de los sistemas integrados de Azure Stack va de los 4 a los 12 nodos. Estos sistemas son compatibles con Microsoft y con los asociados de hardware.  Use los sistemas integrados de Azure Stack para permitir nuevos escenarios para las cargas de trabajo de producción.    

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit
Microsoft Azure Stack Development Kit es una implementación de nodo único de Azure Stack que puede usar para evaluar y conocer Azure Stack.  También puede usar Azure Stack Development Kit como entorno de desarrollo, donde puede desarrollar con API y herramientas coherentes con Azure.  Azure Stack Development Kit no está pensado para usarse como entorno de producción.

Azure Stack Development Kit tiene las siguientes limitaciones:
* Azure Stack Development Kit está asociado a un solo proveedor de identidades de Azure Active Directory o de Servicios de federación de Active Directory. Puede crear varios usuarios en este directorio y asignar las suscripciones a cada usuario.
* Con todos los componentes implementados en un solo equipo, hay recursos físicos limitados disponibles para los recursos de inquilinos. Esta configuración no está pensada para la evaluación del rendimiento o la escala.
* Los escenarios de redes están limitados debido al requisito de host/NIC única.  

## <a name="next-steps"></a>Pasos siguientes
[Características y conceptos principales](azure-stack-key-features.md)

[Azure Stack: Una extensión de Azure (PDF)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

