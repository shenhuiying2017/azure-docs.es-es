---
title: "Descripción de la retención de datos en el entorno de Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describen dos parámetros de configuración que controlan la retención de datos en el entorno Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/09/2018
ms.openlocfilehash: 46e0c4fa25c7d8a56763b80bf7de97c775c7ee99
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="understand-data-retention-in-time-series-insights"></a>Descripción de la retención de datos en Time Series Insights
En este artículo se describen dos parámetros de configuración que inciden en la retención de datos en el entorno Time Series Insights (TSI).

Cada entorno de TSI tiene un parámetro que controla el **tiempo de retención de datos**. El valor abarca de 1 a 400 días. Los datos se eliminan según la capacidad de almacenamiento del entorno o la duración de la retención (1-400), lo que se agote antes.

Cada entorno de TSI tiene una configuración adicional de **Comportamiento correspondiente a un exceso del límite de almacenamiento**. Esta configuración controla el comportamiento de entrada y de purga cuando se alcanza la capacidad máxima de un entorno. Hay dos comportamientos para elegir:
- **Purgar datos antiguos** (valor predeterminado)  
- **Pausar entradas**

> [!NOTE]
> De forma predeterminada, al crear un nuevo entorno, el período de retención está configurado en **Purgar datos antiguos**. Esta opción se puede activar o desactivar según sea necesario después de la creación en Azure Portal, en la página **Configurar** del entorno TSI.

Para obtener información acerca de cómo cambiar los comportamientos de retención, consulte [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configuración de la retención en Time Series Insights).

Compare el comportamiento de retención de datos:

## <a name="purge-old-data"></a>Purgar datos antiguos
- Este comportamiento es el comportamiento predeterminado para los entornos de TSI y exhibe el mismo comportamiento que los entornos de TSI han exhibido desde que se lanzaron en versión preliminar pública.  
- Este comportamiento es preferible cuando los usuarios desean ver siempre sus *datos más recientes* en su entorno de TSI. 
- Este comportamiento *purga* los datos una vez que se alcanzan los límites del entorno (tiempo de retención, tamaño o cantidad, lo que llegue antes). La retención está establecida en 30 días de forma predeterminada. 
- Los datos introducidos hace más tiempo se purgan primero (enfoque FIFO [PEPS]).

### <a name="example-1"></a>Ejemplo 1:
Piense por ejemplo en un entorno que tenga el comportamiento de **continuar entrada y purgar datos antiguos**: en este ejemplo, el **tiempo de retención de datos** está configurado en 400 días. La **capacidad** es una unidad S1, que contiene 30 GB de capacidad total.   Supongamos que los datos entrantes acumulan 500 MB cada día por término medio. Este entorno solo puede retener datos de 60 días dada la tasa de datos entrantes, ya que la capacidad máxima se alcanza a los 60 días. Los datos entrantes se acumulan de este modo: 500 MB cada día x 60 días = 30 GB. 

En este ejemplo, al día 61º, el entorno muestra los datos más actuales, pero purga los más antiguos (los que tengan más de 60 días). La purga genera espacio para el flujo entrante de nuevos datos, de manera que puedan seguir explorándose nuevos datos. 

Si el usuario desea retener los datos durante más tiempo, puede aumentar el tamaño del entorno mediante la adición de unidades adicionales o bien insertar menos datos.  

### <a name="example-2"></a>Ejemplo 2:
Piense ahora en un entorno que tiene configurado también el comportamiento de retención en el modo de **continuar entrada y purgar datos antiguos**. En este ejemplo, el **tiempo de retención de datos** está configurado e en un valor inferior de 180 días. La **capacidad** es una unidad S1, que contiene 30 GB de capacidad total. Con el fin de almacenar los datos de los 180 días completos, la entrada diaria no puede ser superior a 0,166 GB (166 MB) por día.  

En caso de que la tasa de entrada diaria del entorno sea superior a 0,166 GB por día, los datos no se podrán almacenar durante 180 días, ya que algunos datos se purgarán. Piense en este mismo entorno durante un período de tiempo ocupado. Supongamos que la tasa de entrada del entorno puede aumentar hasta un promedio de 0,189 por día. En ese período de tiempo ocupado, se retienen unos 158 días de datos (30GB/0,189 = 158,73 días de retención). Este tiempo es inferior al periodo de tiempo de retención de datos deseado.

## <a name="pause-ingress"></a>Pausar entradas
- Este comportamiento está diseñado para velar por que no se purguen los datos si los límites de tamaño y cantidad se alcanzan con anterioridad a su período de retención.  
- También proporciona más tiempo para que los usuarios aumenten la capacidad de su entorno antes de que los datos se purguen una vez que se supere el periodo de retención.
- Además, ayuda a proteger contra la pérdida de datos, pero genera una oportunidad para la pérdida de los datos más recientes si la entrada se pausa una vez alcanzado el periodo de retención del origen del evento.
- Sin embargo, una vez alcanzada la capacidad máxima de un entorno, el entorno pausa la entrada de datos hasta que ocurran acciones adicionales: 
   - Aumenta la capacidad máxima del entorno. Para obtener más información, consulte [Escalado de su entorno de Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para agregar más unidades de escalado.
   - Se alcanza el período de retención de datos y estos se purgan, poniendo al entorno por debajo de su capacidad máxima.

### <a name="example-3"></a>Ejemplo 3:
Piense en un entorno con el comportamiento de retención configurado en **pausar entradas**. En este ejemplo, el **período de retención de datos** está configurado en 60 días. La **capacidad** se establece en 3 unidades de S1. Supongamos que este entorno tiene una entrada de datos de 2 GB cada día. En este entorno, la entrada se pausa una vez que se alcance la capacidad máxima. En ese momento, el entorno muestra el mismo conjunto de datos hasta que se reanude la entrada o hasta que se habilite "continuar entrada" (lo cual purgaría los datos más antiguos para dejar sitio a los nuevos). 

Cuando se reanuda la entrada:
- Los datos fluyen en el orden en que se recibieron por el origen del evento.
- Los eventos se indexan en función de su marca de tiempo, a menos que se hayan superado las directivas de retención en el origen del evento. Para obtener más información sobre la configuración de la retención del origen del evento, consulte [Preguntas frecuentes sobre Event Hubs](../event-hubs/event-hubs-faq.md).

> [!IMPORTANT]
> Debe establecer alertas para proporcionar un aviso que ayude a evitar que el ingreso se pause. Es posible que se pierdan datos, ya que el tiempo predeterminado de retención es de 1 día para orígenes de eventos de Azure. Por lo tanto, cuando se pausa la entrada, es probable que pierdan los datos más recientes, a menos que se realice alguna acción adicional. Debe aumentar la capacidad, o cambiar el comportamiento a **Purgar datos antiguos** para evitar la posible pérdida de datos.

En los Event Hubs afectados, considere la posibilidad de ajustar la propiedad **Retención de mensajes** para minimizar la pérdida de datos cuando se pausa la entrada en Time Series Insights.

![Retención de mensajes de Event Hub.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Si no se configuran propiedades en el origen del evento (timeStampPropertyName), TSI se establece de manera predeterminada en la marca de tiempo de llegada al Event Hub como eje x. Si se configura timeStampPropertyName en algo diferente, el entorno se fija en el valor timeStampPropertyName configurado en el paquete de datos al analizar eventos. 

Si necesita escalar su entorno para dar cabida a una capacidad adicional o para aumentar la duración de retención, vea [Escalado de su entorno de Time Series Insights](time-series-insights-how-to-scale-your-environment.md) para obtener más información.  

## <a name="next-steps"></a>pasos siguientes
Para obtener información acerca de cómo cambiar los comportamientos de retención, consulte [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configuración de la retención en Time Series Insights).
