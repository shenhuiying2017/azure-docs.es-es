---
title: Solución Service Map en la demostración autodidáctica de Azure | Microsoft Docs
description: Service Map es una solución de Azure que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Se trata de una demostración autodidáctica en la que se examina el uso de Service Map para identificar y diagnosticar un problema simulado en una aplicación web.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>Demostración autodidáctica: Service Map
Se trata de una demostración autodidáctica en la que se examina el uso de la solución [Service Map](monitoring-service-map.md) de Azure para identificar y diagnosticar un problema simulado en una aplicación web. Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. También consolida los datos recopilados por otros servicios y soluciones para ayudarle a analizar el rendimiento y a identificar problemas. Asimismo, usará [búsquedas de registros de Log Analytics](../log-analytics/log-analytics-log-searches.md) para profundizar en los datos recopilados con el fin de identificar la causa principal del problema.


## <a name="scenario-description"></a>Descripción del escenario
Acaba de recibir una notificación de que la aplicación del portal de clientes de ACME está teniendo problemas de rendimiento. La única información que tiene es que estos problemas se iniciaron aproximadamente a las 4:00 a.m. hora del Pacífico del día actual. No está completamente seguro de todos los componentes de los que depende el portal aparte de un conjunto de servidores web. 

## <a name="components-and-features-used"></a>Componentes y características usados
- [Solución Service Map](monitoring-service-map.md)
- [Búsquedas de registros de Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>Tutorial

### <a name="1-connect-to-the-oms-experience-center"></a>1. Conexión a OMS Experience Center
En este tutorial se usa [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/), que proporciona un entorno completo de Log Analytics con datos de ejemplo. Para comenzar, siga este vínculo, proporcione su información y luego seleccione el escenario **Insight and Analytics**.


### <a name="2-start-service-map"></a>2. Inicio de Service Map
Inicie la solución Service Map; para ello, haga clic en el icono **Service Map**.

![Icono de Service Map](media/monitoring-walkthrough-servicemap/tile.png)

Se muestra la consola de Service Map. En el panel izquierdo hay una lista de equipos en su entorno con el agente de Service Map instalado. Seleccionará el equipo que desea ver de esta lista.

![Lista de equipos](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Visualización del equipo
Sabemos que los servidores web se llaman AcmeWFE001 y AcmeWFE002, así que este parece un lugar razonable por dónde empezar. Haga clic en **AcmeWFE001**. Se muestra el mapa de AcmeWFE001 y todas sus dependencias. Puede ver los procesos que se ejecutan en el equipo seleccionado y los servicios externos con los que se comunican.

![Servidor Web](media/monitoring-walkthrough-servicemap/web-server.png)

Le preocupa el rendimiento de la aplicación web, así que haga clic en el proceso **AcmeAppPool (Grupo de aplicaciones de IIS)**. Se muestran los detalles de este proceso y se resaltan sus dependencias. 

![Grupo de aplicaciones](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Cambio de la ventana de tiempo

Ha sabido que el problema se inició a las 4:00 a.m., así que echemos un vistazo a lo que sucedía en ese momento. Haga clic en **Intervalo de tiempo** y cambie la hora a 4:00 a.m. Hora del Pacífico (mantenga la fecha actual y ajuste la zona horaria local) con una duración de 20 minutos.

![Selector de hora](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Visualización de la alerta

Ahora ve que la dependencia **acmetomcat** muestra una alerta, así que este es el posible problema. Haga clic en el icono de alerta de **acmetomcat** para mostrar los detalles de la alerta. Puede ver que hay un uso crítico de la CPU y puede expandirlo para verlo con más detalle. Esta es probablemente la causa de que el rendimiento sea tan lento. 

![Alerta](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Visualización del rendimiento

Vamos a echar un vistazo más de acerca a **acmetomcat**. Haga clic en la parte superior derecha de **acmetomcat** y seleccione **Cargar Service Map** para mostrar los detalles y las dependencias de esta máquina. A continuación, puede examinar más de cerca esos contadores de rendimiento para comprobar nuestra sospecha. Seleccione la pestaña **Rendimiento** para mostrar los [contadores de rendimiento recopilados por Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) durante el intervalo de tiempo. Puede ver que está obteniendo picos periódicos en el procesador y la memoria.

![Rendimiento](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Visualización del seguimiento de cambios
Veamos si podemos descubrir cuál podría ser la causa de esta elevada utilización. Haga clic en la pestaña **Resumen**. Se proporciona información que ha recopilado Log Analytics del equipo, como errores de conexión, alertas críticas y cambios de software. Ya deben haberse expandido secciones con información reciente significativa, y se puede expandir otras secciones para inspeccionar la información que contienen.


Si **Seguimiento de cambios** no está ya abierta, expándala. Se muestra la información recopilada la [solución de seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md). Parece que se ha producido un cambio de software durante este período de tiempo. Haga clic en **Software** para obtener detalles. Se agregó un proceso de copia de seguridad justo después de las 4:00 a.m., así que puede este sea el culpable del consumo excesivo de recursos.

![Seguimiento de cambios](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Visualización de los detalles en la búsqueda de registros
Para comprobaciones adicionales, examine la información detallada sobre el rendimiento recopilada en el área de trabajo de Log Analytics. Haga clic de nuevo en la pestaña **Alertas** y luego en una de las alertas de **uso elevado de la CPU**. Haga clic en **Mostrar en la búsqueda de registro**. Se abre la ventana Búsqueda de registros donde puede realizar [búsquedas de registros](../log-analytics/log-analytics-log-searches.md) en los datos almacenados en el área de trabajo. Service Map ya ha rellenado una consulta para recuperar la alerta en la que estamos interesados. 

![Búsqueda de registros](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Apertura de la búsqueda guardada
Veamos si podemos obtener algunos detalles más sobre la recopilación de rendimiento que generó esta alerta y comprobar la sospecha de que los problemas se deben al proceso de copia de seguridad. Cambie el intervalo de tiempo a **6 horas**. A continuación, haga clic en **Favoritos** y desplácese hasta las búsquedas guardadas para **Service Map**. Ha creado estas consultas específicamente para este análisis. Haga clic en **5 principales procesos por uso de CPU para acmetomcat**.

![Búsqueda guardada](./media/monitoring-walkthrough-servicemap/saved-search.png)


Esta consulta devuelve una lista de los 5 procesos principales que consumen procesador en **acmetomcat**. Puede inspeccionar la consulta para obtener una introducción al lenguaje de consulta usado en las búsquedas de registros. Si lo que le interesaran fueran los procesos en otros equipos, podría modificar la consulta para recuperar esa información.

En este caso, puede ver que el proceso de copia de seguridad consume continuamente un 60 % aproximadamente de CPU del servidor de aplicaciones. Es obvio que este proceso nuevo es responsable de nuestro problema de rendimiento. Obviamente, la solución sería quitar este nuevo software de copia de seguridad del servidor de aplicaciones. En realidad, podría usar Desired State Configuration (DSC) administrada por Azure Automation para definir directivas que garanticen que este proceso nunca se ejecute en estos sistemas críticos.


## <a name="summary-points"></a>Puntos de resumen
- [Service Map](monitoring-service-map.md) le proporciona una vista de toda su aplicación incluso si no conoce todos sus servidores y dependencias.
- Service Map expone los datos recopilados por otras soluciones de administración para ayudarle a identificar problemas con sus aplicaciones y su infraestructura subyacente.
- Las [búsquedas de registros](../log-analytics/log-analytics-log-searches.md) le permiten profundizar en los datos específicos recopilados en el área de trabajo de Log Analytics.  

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Service Map](monitoring-service-map.md).
- [Implementación y configuración de Service Map](monitoring-service-map-configure.md).
- Aprenda sobre [Log Analytics](../log-analytics/log-analytics-overview.md), una aplicación que es necesaria para Service Map y que almacena datos operativos almacenados por los agentes.