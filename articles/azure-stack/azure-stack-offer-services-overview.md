---
title: Ofertas de servicios en Azure Stack | Microsoft Docs
description: Como operador de nube, puede ofrecer servicios a los usuarios.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7a0fe4e02cf7a5feee90c3a0bf5596b44d5f9038
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Introducción a la oferta de servicios en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

[Microsoft Azure Stack](azure-stack-poc.md) es una nueva plataforma en la nube híbrida que le permite proporcionar servicios desde el centro de datos. Como proveedor de servicios, puede ofrecer servicios a los inquilinos. Dentro de una empresa u organismo gubernamental, puede ofrecer servicios locales a los empleados. Los servicios que puede entregar incluyen, entre otros:

- Servicios de plataforma como servicio (PaaS), como App Services, Mobile Apps, API Apps, API Functions, SQL, MySQL.

Incluso puede combinar los servicios para integrar y compilar soluciones complejas para distintos usuarios.

Para ofrecer estos servicios a los usuarios, debe crear [planes, ofertas y cuotas](azure-stack-plan-offer-quota-overview.md). Luego, los usuarios pueden suscribirse a las ofertas para usar los servicios.

## <a name="plan-your-service-offers"></a>Planificar las ofertas de servicios

Cuando planifique las ofertas, tenga en cuenta los puntos siguientes:

**Ofertas de prueba**: puede usar ofertas de prueba para atraer a usuarios nuevos, que luego pueden actualizar a servicios adicionales. Para crear una oferta de prueba, cree un pequeño [plan base](azure-stack-plan-offer-quota-overview.md#base-plan) con un plan de complementos opcional mayor.

**Planificación de capacidad**: es posible que le preocupe que los usuarios acaparen grandes cantidades de recursos y atasquen el sistema para todos los usuarios. Para mejorar el rendimiento, puede [configurar sus planes con cuotas](azure-stack-plan-offer-quota-overview.md#plans) para limitar el uso.

**Proveedores delegados**: puede conceder a otros usuarios la capacidad de crear ofertas en su entorno. Por ejemplo, si es proveedor de servicios, puede [delegar](azure-stack-delegated-provider.md) esta capacidad a sus distribuidores. O bien, si es una organización, puede delegar en otras divisiones o subsidiarias.

## <a name="next-steps"></a>pasos siguientes
[Más información sobre ofertas, planes, cuotas y suscripciones](azure-stack-plan-offer-quota-overview.md)

