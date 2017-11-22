---
title: "Exploración de datos de HockeyApp en Azure Application Insights | Microsoft Docs"
description: "Analice el uso y el rendimiento de la aplicación de Azure con Application Insights."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: bec2b2dc8a29e5c94e3d3c7f2f0333e07bfe3a4f
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Exploración de datos de HockeyApp en Application Insights

> [!NOTE]
> Visual Studio App Center ahora es el servicio recomendado de Microsoft para la supervisión de aplicaciones móviles nuevas. [Aprenda a configurar las aplicaciones con App Center y Application Insights](app-insights-mobile-center-quickstart.md).
> 
> 

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) es un servicio para supervisar aplicaciones de escritorio y móviles activas. En HockeyApp, puede enviar datos de telemetría de seguimiento y personalizados para supervisar el uso y ayudar en el diagnóstico (además de obtener datos de bloqueo). Esta secuencia de datos de telemetría se puede consultar mediante la eficaz característica [Analytics](app-insights-analytics.md) de [Azure Application Insights](app-insights-overview.md). Además, puede [exportar los datos de telemetría de seguimiento y personalizados](app-insights-export-telemetry.md). Para habilitar estas características, configure un puente que transmita los datos personalizados de HockeyApp a Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>La aplicación de puente de HockeyApp
La aplicación de puente de HockeyApp es la característica principal que permite tener acceso a la telemetría personalizada y de seguimiento de HockeyApp en Application Insights mediante las características de análisis y de exportación continua. Los eventos personalizados y de seguimiento que recopile HockeyApp tras la creación de la aplicación de puente de HockeyApp serán accesibles desde estas características. Veamos cómo configurar una de estas aplicaciones de puente.

En HockeyApp, abra Configuración de cuenta, [API Tokens](https://rink.hockeyapp.net/manage/auth_tokens)(Tokens de API). Cree un token o use uno existente. Los derechos mínimos necesarios son "solo lectura". Realice una copia del token de API.

![Obtener un token de API de HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Abra el Portal de Microsoft Azure y [cree un recurso de Application Insights](app-insights-create-new-resource.md). Establezca el Tipo de aplicación en “HockeyApp bridge application” (Aplicación de puente de HockeyApp):

![Nuevo recurso de Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

No es necesario establecer un nombre, se establecerá automáticamente en el nombre de HockeyApp.

Aparecen los campos del puente de HockeyApp. 

![Especifique los campos de puente](./media/app-insights-hockeyapp-bridge-app/03.png)

Escriba el token de HockeyApp que anotó anteriormente. Esta acción rellena el menú desplegable "HockeyApp Application" (Aplicación de HockeyApp) con todas las aplicaciones de HockeyApp. Seleccione la que desee utilizar y complete el resto de los campos. 

Abra el nuevo recurso. 

Tenga en cuenta que los datos tardan un tiempo para empezar a fluir.

![Recurso de Application Insights en espera de datos](./media/app-insights-hockeyapp-bridge-app/04.png)

¡Ya está! Los datos personalizados y de seguimiento recopilados en la aplicación instrumentada por HockeyApp desde este punto en adelante también están disponibles ahora en las características de análisis y exportación continua de Application Insights.

Revisemos brevemente cada una de estas características que ahora tiene disponibles.

## <a name="analytics"></a>Análisis
Analytics es una herramienta eficaz para realizar consultas ad-hoc de los datos, lo que permite diagnosticar y analizar los datos de telemetría y detectar rápidamente las causas raíz y patrones.

![Análisis](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Aprenda más acerca de Analytics](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>Exportación continua
Exportación continua permite exportar los datos en un contenedor de Azure Blob Storage. Esto es muy útil si necesita mantener los datos durante más tiempo que el período de retención ofrecido actualmente por Application Insights. Puede mantener los datos en el almacenamiento de blobs, procesarlos en una instancia de SQL Database o usar su solución de almacenamiento de datos preferida.

[Aprenda más acerca de la exportación continua](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Pasos siguientes
* [Aplicar Analytics a los datos](app-insights-analytics-tour.md)

