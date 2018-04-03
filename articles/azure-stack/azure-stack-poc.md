---
title: ¿Qué es Azure Stack? | Microsoft Docs
description: Azure Stack le permite ejecutar servicios de Azure en su centro de datos.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 863c1ec562cd71af0df69ccc0547e16d02c7ee82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-stack"></a>¿Qué es Azure Stack?

Microsoft Azure Stack es una plataforma en la nube híbrida que le permite proporcionar servicios de Azure desde el centro de datos de la organización.  Azure Stack está diseñado para posibilitar nuevos escenarios para las aplicaciones modernas en escenarios clave, como los entornos perimetrales y los entornos desconectados, así como para resolver determinados requisitos de seguridad y de cumplimiento.  Azure Stack se ofrece en dos opciones de implementación para satisfacer sus necesidades.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados de Azure Stack
Los sistemas integrados de Azure Stack se suministran mediante una asociación de Microsoft y [de asociados de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), con lo que se crea una solución que ofrece innovación dirigida a la nube y equilibrada con simplicidad en la administración.  Dado que Azure Stack se suministra como un sistema integrado de hardware y software, se le ofrece la flexibilidad y el control adecuados, a la vez que puede seguir adoptando la innovación de la nube.  El tamaño de los sistemas integrados de Azure Stack va de los 4 a los 12 nodos. Estos sistemas son compatibles con Microsoft y con los asociados de hardware.  Use los sistemas integrados de Azure Stack para permitir nuevos escenarios para las cargas de trabajo de producción.    

## <a name="azure-stack-development-kit"></a>Kit de desarrollo de Azure Stack
El [Kit de desarrollo de Azure Stack](.\asdk\asdk-what-is.md) es una implementación de nodo único de Azure Stack que puede usar para evaluar y conocer Azure Stack.  También puede usar el Kit de desarrollo de Azure Stack como entorno de desarrollo, donde puede desarrollar con API y herramientas coherentes con Azure. El Kit de desarrollo de Azure Stack no está diseñado para ejecutarse en un entorno de producción.

El Kit de desarrollo de Azure Stack tiene las siguientes limitaciones:
* El Kit de desarrollo de Azure Stack está asociado a un solo proveedor de identidades de Azure Active Directory (Azure AD) o de Servicios de federación de Active Directory (AD FS). Puede crear varios usuarios en este directorio y asignar las suscripciones a cada usuario.
* Con todos los componentes implementados en un solo equipo, hay recursos físicos limitados disponibles para los recursos de inquilinos. Esta configuración no está pensada para la evaluación del rendimiento o la escala.
* Los escenarios de redes están limitados debido al requisito de host/NIC única.  

## <a name="next-steps"></a>Pasos siguientes
[Características y conceptos principales](azure-stack-key-features.md)

[Azure Stack: Una extensión de Azure (PDF)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

