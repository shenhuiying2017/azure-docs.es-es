---
title: "Configuración de orígenes de datos en Azure Log Analytics | Microsoft Docs"
description: "Los orígenes de datos definen los datos que Log Analytics recopila de agentes y otros orígenes conectados.  En este artículo se describe el concepto de cómo Log Analytics usa los orígenes de datos, se explican los detalles de cómo configurarlos y se brinda un resumen de los distintos orígenes de datos disponibles."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: bwren
ms.openlocfilehash: 4237df0934d6191b77ff82c86a66585e72191ac9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="data-sources-in-log-analytics"></a>Orígenes de datos en Log Analytics
Log Analytics recopila datos de los orígenes conectados y los almacena en el área de trabajo de Log Analytics.  Los orígenes de datos que configura definen los datos que se recopilan de cada uno de ellos.  Los datos de Log Analytics se almacenan como un conjunto de registros.  Cada origen de datos crea registros de un tipo determinado, donde cada tipo tiene su propio conjunto de propiedades.

![Recopilación de datos de Log Analytics](./media/log-analytics-data-sources/overview.png)

Los orígenes de datos son distintos de las [soluciones de administración](log-analytics-add-solutions.md), que también recopilan datos de los orígenes conectados y crean registros en Log Analytics.  Además de recopilar datos, las soluciones incluyen normalmente búsquedas y vistas de registros que ayudan a analizar el funcionamiento de una aplicación o servicio determinados.


## <a name="summary-of-data-sources"></a>Resumen de orígenes de datos
En la tabla siguiente se enumeran los orígenes de datos que actualmente se encuentran disponibles en Log Analytics.  Cada uno de ellos tiene un vínculo a un artículo independiente, donde se proporcionan detalles con respecto al origen de datos determinado.

| Origen de datos | Tipo de evento | DESCRIPCIÓN |
|:--- |:--- |:--- |
| [Registros personalizados](log-analytics-data-sources-custom-logs.md) |\<nombreDeRegistro\>_CL |Archivos de texto en agentes de Windows o Linux que contienen información de registro. |
| [Registros de eventos de Windows](log-analytics-data-sources-windows-events.md) |Evento |Eventos recopilados del registro de eventos en los equipos Windows. |
| [Contadores de rendimiento de Windows](log-analytics-data-sources-performance-counters.md) |Perf |Contadores de rendimiento recopilados de equipos con Windows. |
| [Contadores de rendimiento de Linux](log-analytics-data-sources-performance-counters.md) |Perf |Contadores de rendimiento recopilados de equipos con Linux. |
| [Registros de IIS](log-analytics-data-sources-iis-logs.md) |W3CIISLog |Registros de Internet Information Services en el formato W3C. |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Eventos de Syslog en equipos con Windows o Linux. |

## <a name="configuring-data-sources"></a>Configuración de orígenes de datos
Los orígenes de datos se configuran en el menú **Datos** en **Configuración avanzada** de Log Analytics.  Cualquier configuración se proporciona a todos los orígenes conectados del área de trabajo.  Actualmente, no puede excluir ningún agente de esta configuración.

![Configurar eventos de Windows](./media/log-analytics-data-sources/configure-events.png)

1. En Azure Portal, seleccione **Log Analytics** > el área de trabajo > **Configuración avanzada**.
2. Seleccione **Datos**.
3. Haga clic en el origen de datos que quiere configurar.
4. Siga el vínculo a la documentación correspondiente a cada origen de datos de la tabla anterior para obtener detalles sobre su configuración.


## <a name="data-collection"></a>Colección de datos
Las configuraciones de orígenes de datos se entregan en cuestión de minutos a los agentes que están directamente conectados con Log Analytics.  Los datos especificados se recopilan desde el agente y se entregan directamente a Log Analytics a intervalos específicos a cada origen de datos.  Consulte la documentación de cada origen de datos para ver estas especificaciones.

En el caso de los agentes de System Center Operations Manager de un grupo de administración conectado, la configuración de origen de datos se convierte en módulos de administración y se proporciona al grupo de administración cada 5 minutos, de manera predeterminada.  El agente descarga el módulo de administración de la forma habitual y recopila los datos especificados. En función del origen de datos, los datos se enviarán a un servidor de administración que los reenvía a Log Analytics o el agente los enviará a Log Analytics sin pasar por el servidor de administración. Para más información, consulte los [detalles de la recopilación de datos](log-analytics-add-solutions.md#data-collection-details).  Puede leer detalles sobre cómo conectar Operations Manager y Log Analytics y modificar la frecuencia con que la configuración se proporciona en [Configuración de integración con System Center Operations Manager](log-analytics-om-agents.md).

Si el agente no puede conectarse a Log Analytics u Operations Manager, continuará recopilando datos para entregarlos cuando se establezca la conexión.  Si el volumen de datos alcanza el tamaño máximo de la memoria caché del cliente o si el agente no es capaz de establecer conexión en un plazo de 24 horas, los datos pueden perderse.

## <a name="log-analytics-records"></a>Registros de Log Analytics
Todos los datos que recopila Log Analytics se almacenan en el área de trabajo como registros.  Los registros que recopilan distintos orígenes de datos tendrán su propio conjunto de propiedades y se identificar por su propiedad **Type** .  Consulte la documentación de cada origen de datos y solución para obtener detalles sobre cada tipo de registro.

## <a name="next-steps"></a>pasos siguientes
* Conozca las [soluciones](log-analytics-add-solutions.md) que agregan funcionalidad a Log Analytics y que también recopilan datos del área de trabajo.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de soluciones y orígenes de datos.  
* Configure [alertas](log-analytics-alerts.md) que le notifiquen de manera proactiva acerca de los datos críticos recopilados de soluciones y orígenes de datos.
