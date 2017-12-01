---
title: "Adición de un origen de eventos del Centro de eventos a Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describe cómo agregar un origen de evento que está conectado a un centro de eventos a su entorno de Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/21/2017
ms.openlocfilehash: c07c847784eb13c62e350e9c655e027e7df696a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Adición de un origen de evento de centro de eventos al entorno de Time Series Insights

En este artículo se describe cómo usar Azure Portal para agregar un origen de evento que lea datos de un centro de eventos al entorno de Time Series Insights.

## <a name="prerequisites"></a>Requisitos previos
- Cree el entorno de Time Series Insights. Para más información, consulte [Creación de un entorno de Azure Time Series Insights](time-series-insights-get-started.md). 
- Cree un centro de eventos. Para más información sobre Event Hubs, consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md).
- El centro de eventos debe tener eventos de mensajes activos en proceso de envío. Para más información, consulte [Envío de eventos a Azure Event Hubs mediante .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Cree un grupo de consumidores dedicado en el centro de eventos para que el entorno de Time Series Insights los consuma. Cada origen del evento de Time Series Insights tiene que tener su propio grupo de consumidores dedicado que no se comparte con ningún otro consumidor. Si varios lectores consumen eventos desde el mismo grupo de consumidores, es probable que todos los lectores vean errores. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por Centro de eventos. Para obtener detalles, consulte la [Guía de programación de Event Hubs](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Adición de un nuevo origen del evento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Busque su entorno de Time Series Insights existente. Haga clic en **Todos los recursos** en el menú izquierdo de Azure Portal. Seleccione el entorno de Time Series Insights.

3. En el encabezado **Environment Topology** (Topología del entorno), haga clic en **Orígenes de eventos**.

   ![Orígenes de eventos + Agregar](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Haga clic en **+ Agregar**.

5. Proporcione un **nombre de origen de evento** único para este entorno de Time Series Insights, como **flujo de eventos**.

   ![Rellene los tres primeros parámetros del formulario.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. En **Origen**, seleccione **Centro de eventos**.

7. Seleccione la **opción de importación** adecuada. 
   - Elija **Use Event Hub from available subscriptions** (Usar el centro de eventos de las suscripciones disponibles) cuando ya tenga un centro de eventos existente en una de sus suscripciones. Este es el enfoque más sencillo.
   - Elija **Indicar manualmente la configuración del Centro de eventos** cuando el centro de eventos sea externo a sus suscripciones o si quiere elegir opciones avanzadas. 

8. Si ha seleccionado la opción **Use Event Hub from available subscriptions** (Usar el centro de eventos de las suscripciones disponibles), en la siguiente tabla se explica cada propiedad necesaria:

   ![Detalles del centro de evento y la suscripción](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Propiedad | Descripción |
   | --- | --- |
   | Id. de suscripción | Seleccione la suscripción en la que se creó el centro de eventos.
   | Espacio de nombres de Service bus | Seleccione el espacio de nombres de Service Bus que contiene el centro de eventos.
   | Nombre del centro de eventos | Seleccione el nombre del centro de eventos.
   | Nombre de la directiva del centro de eventos | Seleccione directiva de acceso compartido, que se puede crear en la pestaña Configuración de centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
   | Clave de la directiva de centro de eventos | El valor de clave se puede rellenar previamente.
   | Grupo de consumidores de centro de eventos | El grupo de consumidores para leer eventos del centro de eventos. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento. |
   | Formato de serialización de eventos | Por el momento, JSON es la única serialización disponible. Los mensajes de eventos deben estar en este formato, o bien no se podrá leer ningún dato. |
   | Nombre de la propiedad de marca de tiempo | Para determinar este valor, debe comprender el formato de mensaje de los datos del mensaje enviados al centro de eventos. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |


9. Si ha seleccionado la opción **Indicar manualmente la configuración del Centro de eventos**, en la tabla siguiente se explica cada propiedad necesaria:

   | Propiedad | Descripción |
   | --- | --- |
   | Id. de suscripción | La suscripción en la que se creó este centro de eventos.
   | Grupos de recursos | El grupo de recursos en el que se creó este centro de eventos.
   | Espacio de nombres de Service bus | Un espacio de nombres de Service Bus es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres de Service Bus.
   | Nombre del centro de eventos | El nombre del centro de eventos. Cuando creó el centro de eventos, también le asignó un nombre específico.
   | Nombre de la directiva del centro de eventos | La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
   | Clave de la directiva de centro de eventos | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres de Service Bus. Escriba la clave principal o secundaria aquí.
   | Grupo de consumidores de centro de eventos | El grupo de consumidores para leer eventos desde el centro de eventos. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.
   | Formato de serialización de eventos | Por el momento, JSON es la única serialización disponible. Los mensajes de eventos deben estar en este formato, o bien no se podrá leer ningún dato. |
   | Nombre de la propiedad de marca de tiempo | Para determinar este valor, debe comprender el formato de mensaje de los datos del mensaje enviados al centro de eventos. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |


10. Seleccione **Crear** para agregar el nuevo origen del evento.
   
   ![Click Create](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Tras la creación del origen de eventos, Time Series Insights iniciará automáticamente la transmisión de datos al entorno.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Adición de un grupo de consumidores al centro de eventos
Las aplicaciones usan grupos de consumidores para extraer datos de Azure Event Hubs. Proporcione un grupo de consumidores dedicado, solo para su uso en este entorno de Time Series Insights, para leer datos de manera confiable del centro de eventos.

Para agregar un nuevo grupo de consumidores a su centro de eventos, siga estos pasos:
1. En Azure Portal, busque y abra el centro de eventos.

2. En el encabezado **Entidades**, seleccione **Grupos de consumidores**.

   ![Centro de eventos - Agregar un grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Seleccione **+ Grupo de consumidores** para agregar un nuevo grupo de consumidores. 

4. En la página **Grupos de consumidores**, proporcione un nuevo **nombre** único.  Use este mismo nombre al crear un nuevo origen del evento en el entorno de Time Series Insights.

5. Seleccione **Crear** para crear el nuevo grupo de consumidores.

## <a name="next-steps"></a>Pasos siguientes
- [Defina las directivas de acceso a datos](time-series-insights-data-access.md) para proteger los datos.
- [Envíe eventos](time-series-insights-send-events.md) al origen de eventos.
- Acceso al entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
