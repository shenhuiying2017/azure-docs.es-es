---
title: Introducción a los diagnósticos de Azure App Service | Microsoft Docs
description: Aprenda a solucionar los problemas con su aplicación web con los diagnósticos de App Service.
keywords: app service, azure app service, diagnósticos, compatibilidad, aplicación web, solución de problemas, autoayuda
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 50e0e9f5edc18aac42ee80e232f70e09736124bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Introducción a los diagnósticos de Azure App Service 

Cuando ejecute una aplicación web, querrá estar preparado para los posibles problemas que puedan surgir, desde errores 500 hasta que sus usuarios le comuniquen que su sitio está fuera de servicio. Los diagnósticos de App Service son una experiencia inteligente e interactiva que le ayuda a solucionar los problemas de su aplicación web sin necesidad de configuración alguna. Cuando surgen problemas con la aplicación web, los diagnósticos de App Service le indicarán lo que pasa y le guiarán a la información correcta de forma que pueda solucionar el problema de la manera más fácil y rápida. 
 
Aunque esta experiencia es principalmente útil si tiene problemas con la aplicación web en las últimas 24 horas, todos los gráficos de diagnóstico están disponibles para su análisis en cualquier momento. Se pueden encontrar herramientas de solución de problemas adicionales y vínculos a documentación y foros útiles en la columna derecha.

El diagnóstico de App Service funciona no solo para la aplicación en Windows, sino también para las aplicaciones en [Linux/contenedores](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro), el [entono de App Service](https://docs.microsoft.com/en-us/azure/app-service/environment/intro) y [Azure functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Apertura de los diagnósticos de App Service

Para acceder al diagnóstico de App Service, vaya a la aplicación de App Service o del entorno de App Service en [Azure Portal](https://portal.azure.com). En el panel izquierdo, haga clic en **Diagnosticar y solucionar problemas**. 

Para Azure Functions, vaya a la aplicación de la función y, en el panel de navegación superior, haga clic en **Características de la plataforma** y seleccione **Diagnosticar y solucionar problemas de** en la sección **Supervisión**. 

![Página principal](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Comprobación de mantenimiento

Si no sabe cuál es el problema con la aplicación web o no sabe por dónde empezar a solucionarlo, un buen lugar para comenzar es la comprobación de mantenimiento. La comprobación de mantenimiento analizará las aplicaciones web para ofrecerle una visión general rápida e interactiva de lo que funciona bien y mal y dónde mirar para investigar el problema. Su interfaz inteligente e interactiva le proporciona una guía por el proceso de solución de problemas.  

![Comprobación de mantenimiento](./media/app-service-diagnostics/HealthCheckup2.png)

Si se detecta un problema de una categoría determinada en las últimas 24 horas, puede ver el informe de diagnóstico completo y los diagnósticos de App Service pueden pedirle que vea más consejos de solución de problemas para guiarle mejor en su solución.

![Solución de problemas y siguientes pasos](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Accesos directos de iconos

Si sabe exactamente qué tipo de información de solución de problemas busca, los accesos directos de iconos le llevan directamente al informe de diagnóstico completo de la categoría del problema que le interesa. En comparación con la comprobación de mantenimiento, los accesos directos de iconos son la manera más directa y menos guiada de acceder a las métricas de diagnóstico. Como parte de los métodos abreviados de mosaico,aquí también se pueden buscar **herramientas de diagnóstico**, que son herramientas más avanzadas que le ayudarán a investigar, por ejemplo, problemas relativos a problemas de código de aplicación, lentitud o cadenas de conexión. 

![Accesos directos de iconos](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Informe de diagnóstico

Si desea más información después de ejecutar una [comprobación de mantenimiento](#health-checkup) o ha hecho clic en uno de los [accesos directos de iconos](#tile-shortcuts), el informe de diagnóstico completo le muestra el gráfico de las métricas de interés de las últimas 24 horas. Si la aplicación experimenta tiempo de inactividad, se representa mediante una barra naranja debajo de la escala de tiempo. Puede seleccionar una de las barras de color naranja para seleccionar el tiempo de inactividad y ver las observaciones sobre ese tiempo de inactividad y los pasos de solución de problemas sugeridos. 

![Informe de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Investigación de los problemas de código de aplicación

Dado que muchos problemas de la aplicación están relacionados con el código de esta, el diagnóstico de App Service se integra con [Application Insights](https://azure.microsoft.com/services/application-insights/) para resaltar las excepciones y los problemas de dependencia para ponerlos en correlación con el tiempo de inactividad seleccionado. Application Insights deben estar habilitado por separado. 

Para ver las excepciones y las dependencias de Application Insights, seleccione los métodos abreviados de mosaico **Aplicación web fuera de servicio** o **Aplicación web lenta**. 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

