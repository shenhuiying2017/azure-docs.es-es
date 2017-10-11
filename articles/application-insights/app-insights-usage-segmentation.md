---
title: "Análisis de usuarios, sesiones y evento en Azure Application Insights | Microsoft Docs"
description: "Este artículo trata sobre el análisis de los usuarios de su aplicación web."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: b154a01d1690bff4950ebc1ff5a5b89894d4d111
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análisis de usuarios, sesiones y eventos en Application Insights

Descubra cuándo los usuarios utilizan la aplicación web, en qué páginas que están más interesados, en qué ubicación se encuentran dichos usuarios, y los sistemas operativos y exploradores que emplean. Analice la telemetría de uso y de negocio con [Azure Application Insights](app-insights-overview.md).

## <a name="get-started"></a>Introducción

Si aún no ve los datos en las hojas de usuarios, sesiones o eventos de Application Insights, [obtenga información sobre cómo empezar a trabajar con las herramientas de uso](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>La herramienta de segmentación de usuarios, sesiones y eventos

Tres de las hojas de uso usan la misma herramienta para segmentar y desglosar telemetría de su aplicación web desde tres perspectivas. Mediante el filtrado y la división de los datos, puede descubrir información detallada sobre el uso relativo de distintas páginas y características.

* **Herramienta de usuarios**: averigüe cuántas personas usan la aplicación y sus características.  Los usuarios se cuentan mediante el uso de identificadores anónimos almacenados en las cookies del explorador. Una sola persona que usa distintos exploradores o máquinas se contarán como más de un usuario.
* **Herramienta de sesiones**: la cantidad de sesiones de actividad de usuario que han incluido determinadas páginas y características de la aplicación. Una sesión se cuenta después de media hora de inactividad del usuario, o después de 24 horas de uso continuas.
* **Herramienta de eventos**: la frecuencia con la que se usan ciertas páginas y características de la aplicación. Una vista de página se cuenta cuando un explorador carga una página de la aplicación, siempre que se haya [instrumentado](app-insights-javascript.md). 

    Un evento personalizado representa una repetición de algo que sucede en la aplicación, a menudo, una interacción del usuario como un clic en el botón o la realización de alguna tarea. Inserte código en su aplicación para [generar eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).

![Herramienta de uso](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Consulta de determinados usuarios 

Explore distintos grupos de usuarios mediante ajustando las opciones de consulta en la parte superior de la herramienta Usuarios: 

* que usaron: elija eventos personalizados y vistas de página. 
* Durante: elija un intervalo de tiempo. 
* Por: elija cómo se desglosarán los datos, por un periodo u otra propiedad como un explorador o una ciudad. 
* Dividir por: elija una propiedad por la que dividir o segmentar los datos. 
* Agregar filtros: limite la consulta a determinados usuarios, sesiones o eventos en función de sus propiedades, como un explorador o una ciudad. 
 
## <a name="saving-and-sharing-reports"></a>Guardado e intercambio de informes 
Puede guardar los informes de Usuarios, de forma privada (solo para usted) en la sección Mis informes, o bien con todos aquellos que tengan acceso a este recurso de Application Insights en la sección Informes compartidos.  
 
Al guardar un informe o editar sus propiedades, elija "Intervalo de tiempo relativo actual" para guardar un informe con los datos continuamente actualizados, con lo que puede consultar el periodo que desee.  
 
Elija "Intervalo de tiempo absoluto actual" para guardar un informe con un conjunto fijo de datos. Tenga en cuenta que solo se almacenan datos de Application Insights durante 90 días, por lo que, si han pasado más de 90 días desde que guardó un informe con un intervalo de tiempo absoluto, el informe se mostrará vacío. 
 
## <a name="example-instances"></a>Instancias de ejemplo

La sección de instancias en el ejemplo muestra información sobre una serie de eventos, sesiones o usuarios que coinciden con la consulta actual. Al tener en cuenta y explorar los comportamientos de los usuarios, además de los agregados, se puede proporcionar información sobre cómo se utiliza realmente la aplicación. 
 
## <a name="insights"></a>Información detallada 

La barra lateral de Información detallada muestra grandes grupos los usuarios que comparten propiedades comunes. Estos grupos pueden descubrir tendencias sorprendentes sobre cómo se usa la aplicación. Por ejemplo, si el 40 % de todo el uso de la aplicación proviene de personas que usan una sola característica.  


## <a name="next-steps"></a>Pasos siguientes
- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [vistas de páginas](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    - [Embudos](usage-funnels.md)
    - [Retención](app-insights-usage-retention.md)
    - [Flujos de usuario](app-insights-usage-flows.md)
    - [Libros](app-insights-usage-workbooks.md)
    - [Adición de contexto de usuario](app-insights-usage-send-user-context.md)

