---
title: "Introducción a los diagnósticos de Azure App Service | Microsoft Docs"
description: "Aprenda a solucionar los problemas con su aplicación web con los diagnósticos de App Service."
keywords: "app service, azure app service, diagnósticos, compatibilidad, aplicación web, solución de problemas, autoayuda"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: f027e7fbc5866a85e7f55460192a1c99a71e368e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="azure-app-service-diagnostics-overview"></a>Introducción a los diagnósticos de Azure App Service 

Cuando ejecute una aplicación web, querrá estar preparado para los posibles problemas que puedan surgir, desde errores 500 hasta que sus usuarios le comuniquen que su sitio está fuera de servicio. Los diagnósticos de App Service son una experiencia inteligente e interactiva que le ayuda a solucionar los problemas de su aplicación web sin necesidad de configuración alguna. Cuando surgen problemas con la aplicación web, los diagnósticos de App Service le indicarán lo que pasa y le guiarán a la información correcta de forma que pueda solucionar el problema de la manera más fácil y rápida. 
 
Aunque esta experiencia es principalmente útil si tiene problemas con la aplicación web en las últimas 24 horas, todos los gráficos de diagnóstico están disponibles para su análisis en cualquier momento. Se pueden encontrar herramientas de solución de problemas adicionales y vínculos a documentación y foros útiles en la columna derecha.

![Página principal](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Comprobación de mantenimiento

Si no sabe cuál es el problema con la aplicación web o no sabe por dónde empezar a solucionarlo, un buen lugar para comenzar es la comprobación de mantenimiento. La comprobación de mantenimiento analizará las aplicaciones web para ofrecerle una visión general rápida e interactiva de lo que funciona bien y mal y dónde mirar para investigar el problema. Su interfaz inteligente e interactiva le proporciona una guía por el proceso de solución de problemas.  

![Comprobación de mantenimiento](./media/app-service-diagnostics/HealthCheckup2.png)

Si se detecta un problema de una categoría determinada en las últimas 24 horas, puede ver el informe de diagnóstico completo y los diagnósticos de App Service pueden pedirle que vea más consejos de solución de problemas para guiarle mejor en su solución.

![Solución de problemas y siguientes pasos](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Accesos directos de iconos

Si sabe exactamente qué tipo de información de solución de problemas busca, los accesos directos de iconos le llevan directamente al informe de diagnóstico completo de la categoría del problema que le interesa. En comparación con la comprobación de mantenimiento, los accesos directos de iconos son la manera más directa y menos guiada de acceder a las métricas de diagnóstico.  

![Accesos directos de iconos](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Informe de diagnóstico

Si desea más información después de ejecutar una [comprobación de mantenimiento](#health-checkup) o ha hecho clic en uno de los [accesos directos de iconos](#tile-shortcuts), el informe de diagnóstico completo le muestra el gráfico de las métricas de interés de las últimas 24 horas. Si la aplicación experimenta tiempo de inactividad, se representa mediante una barra naranja debajo de la escala de tiempo. Puede seleccionar uno de los tiempos de inactividad para obtener observaciones analizadas sobre el tiempo de inactividad y las soluciones sugeridas. 

![Informe de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)

## <a name="open-app-service-diagnostics"></a>Apertura de los diagnósticos de App Service

Para acceder a los diagnósticos de App Service, vaya a la aplicación web de App Service en [Azure Portal](https://portal.azure.com). 

En el panel izquierdo, haga clic en **Diagnosticar y solucionar problemas**.