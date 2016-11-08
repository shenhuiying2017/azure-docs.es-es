---
title: Solución de seguimiento de cambios en Log Analytics | Microsoft Docs
description: Puede usar la solución de seguimiento de cambios de configuración de Log Analytics para facilitar el proceso de identificación de los cambios de los servicios de Windows y software que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento.
services: operations-management-suite
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="change-tracking-solution-in-log-analytics"></a>Solución de seguimiento de cambios de Log Analytics
Puede usar la solución de seguimiento de cambios de configuración de Log Analytics para facilitar el proceso de identificación de los cambios de los servicios de Windows, el software y el demonio de Linux que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento.

La solución se instala para actualizar el tipo de agente que ha instalado. Los cambios en los servicios de Windows y el software instalados en los servidores supervisados se leen y, a continuación, los datos se envían al servicio de Log Analytics en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Cuando se detectan cambios, se muestran los servidores con los cambios en el panel de seguimiento de cambios. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Se necesita Operations Manager para la solución de seguimiento de cambios.
* Debe tener un agente de Operations Manager o de Windows en cada equipo en el que desee supervisar los cambios.
* Agregue la solución de seguimiento de cambios al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.

## <a name="change-tracking-data-collection-details"></a>Detalles de la recopilación de datos de seguimiento de cambios
El seguimiento de cambios recopila inventario de software y metadatos de servicio de Windows con los agentes habilitados.

La siguiente tabla muestra los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para el seguimiento de cambios.

| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![No](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png) |cada hora |

## <a name="use-change-tracking"></a>Uso de seguimiento de cambios
Una vez instalada la solución, puede ver el resumen de los cambios para los servidores supervisados mediante el icono **Seguimiento de cambios** en la página **Información general** de OMS.

![imagen del icono de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

* Cambios realizados por el tipo de configuración para los servicios de Windows y el software
* Cambios de software en aplicaciones y actualizaciones para los servidores individuales
* Número total de cambios de software para cada aplicación
* Cambios de servicio de Windows para servidores individuales

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking01.png)

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver los cambios de algún tipo de cambio
1. En la página **Información general**, haga clic en el icono **Seguimiento de cambios**.
2. En el panel **Seguimiento de cambios**, revise la información de resumen en una de las hojas de tipo de cambio y, a continuación, haga clic en una para obtener información detallada acerca de la misma en la página de **búsqueda de registros**.
3. En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre el seguimiento de cambios.

<!--HONumber=Oct16_HO2-->


