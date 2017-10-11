---
title: "Implementación de servicios de Azure API Management en varias regiones de Azure | Microsoft Docs"
description: "Aprenda a implementar una instancia del servicio Administración de API de Azure en varias regiones de Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 1c39fee739c2f5fd4b928e1e76e1ea57f072b5f8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Implementación de una instancia del servicio Administración de API de Azure en varias regiones de Azure
Administración de API admite la implementación en varias regiones, lo que permite a los publicadores de API distribuir un único servicio de Administración de API en el número de regiones de Azure deseado. Esto ayuda a reducir la latencia de solicitud que perciben los usuarios de API distribuidos geográficamente y, además, mejora la disponibilidad del servicio en caso de que una región se quede sin conexión. 

Al crear inicialmente un servicio de API Management, este contiene solo una [unidad][unit] y reside en una sola región de Azure, designada como región primaria. Pueden agregarse otras regiones fácilmente mediante Azure Portal. El servidor de puerta de enlace de API Management se implementa en cada región y el tráfico de llamada se enruta a la puerta de enlace más cercana. Cuando una región se queda sin conexión, el tráfico se redirige automáticamente a la siguiente puerta de enlace más cercana. 

> [!IMPORTANT]
> La implementación en varias regiones solo está disponible en el nivel **[Premium][Premium]**.
> 
> 

## <a name="add-region"> </a>Implementación de una instancia del servicio Administración de API en una nueva región
> [!NOTE]
> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].
> 
> 

En Azure Portal, vaya a la página de **escala y precios** de su instancia de servicio de API Management. 

![Pestaña Escala][api-management-scale-service]

Para implementar en una nueva región, haga clic en **+ Agregar región** desde la barra de herramientas.

![Agregar región][api-management-add-region]

Seleccione la ubicación en la lista desplegable y establezca el número de unidades para el control deslizante.

![Especificar unidades][api-management-select-location-units]

Haga clic en **Agregar** para colocar la selección en la tabla de ubicaciones. 

Repita este proceso hasta que haya configurado todas las ubicaciones y haga clic en **Guardar** en la barra de herramientas para iniciar el proceso de implementación.

## <a name="remove-region"> </a>Eliminación de una instancia de servicio de API Management de una ubicación
En Azure Portal, vaya a la página de **escala y precios** de su instancia de servicio de API Management. 

![Pestaña Escala][api-management-scale-service]

Para la ubicación que desee quitar, abra el menú contextual mediante el botón **...** situado a la derecha de la tabla. Haga clic en la opción **Eliminar**.

![Quitar región][api-management-remove-region]

Confirme la eliminación y haga clic en **Guardar** para aplicar los cambios.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

