---
title: "Solución de problemas de vistas de costos empresariales: Azure | Microsoft Docs"
description: "Obtenga información para resolver los problemas que podría tener con vistas de costos de organización dentro de Azure Portal."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 54c7610f1a0d3de2503ef471ca9adc0db423f530
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="troubleshoot-enterprise-cost-views"></a>Solución de problemas de vistas de costos empresariales 

Dentro de las inscripciones empresariales, hay varias configuraciones que podrían provocar que los usuarios de la inscripción no pudieran ver los costos.  Estas configuraciones las administra el administrador de inscripciones o el asociado si la inscripción no se ha adquirido directamente con Microsoft.  Este artículo le ayuda a comprender cuáles son las configuraciones y de qué manera pueden afectar a la inscripción. Estas opciones son independientes de los [roles de RBAC de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 


## <a name="enabling-access-to-costs"></a>Habilitación del acceso a los costos

¿Aparece un mensaje de acceso no autorizado o que dice que *las vistas de costos están deshabilitadas en su inscripción?* cuando busca información de costos? ![no autorizado](media/billing-enterprise-mgmt-groups/unauthorized.png)

Esto podría deberse a uno de los siguientes motivos:

1. Adquirió Azure a través de un asociado empresarial y dicho asociado todavía no ha publicado la información de los precios. Para liberar los precios, póngase en contacto con su asociado para actualizar la configuración dentro de [Enterprise portal](https://ea.azure.com).
2. O bien, si es un cliente de directo de EA, hay un par de posibilidades:
    * Es el propietario de la cuenta y su administrador de inscripción ha deshabilitado la opción para que el propietario de la cuenta vea los cargos.  
    * Es el administrador del departamento y su administrador de inscripción ha deshabilitado la opción para que el administrador del departamento vea los cargos.
    * Póngase en contacto con el administrador de inscripciones para acceder. El administrador de inscripciones puede visitar [Enterprise Portal](https://ea.azure.com/manage/enrollment) y actualizar la configuración tal y como se muestra aquí:

![Configuración de Enterprise Portal](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>¿No está disponible el recurso? 
Si recibe el mensaje de error "Este recurso no está disponible" al intentar acceder a una suscripción o un grupo de administración, significa que no tiene el rol correcto para ver este elemento.  

![recurso no encontrado](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Póngase en contacto con el administrador de la suscripción o de los grupos de administración para conseguir acceso.  
* Para las suscripciones, consulte el documento [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) para obtener ayuda sobre qué rol se necesita.
