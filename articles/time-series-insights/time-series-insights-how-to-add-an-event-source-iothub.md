---
title: "Adición de un origen de eventos de IoT Hub a Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describe cómo agregar un origen de evento que está conectado a un centro de IoT Hub a su entorno de Time Series Insights."
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
ms.openlocfilehash: 0469c35056d1d02457c162b8540af472b84f1e92
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Adición de un origen de eventos de IoT Hub al entorno de Time Series Insights
Este tutorial describe cómo usar Azure Portal para agregar un origen de eventos que se lea desde un centro de IoT Hub a su entorno de Time Series Insights.

## <a name="prerequisites"></a>Requisitos previos
- Cree el entorno de Time Series Insights. Para obtener más información, consulte [Creación de un entorno de Azure Time Series Insights](time-series-insights-get-started.md). 
- Cree un centro de IoT Hub. Para obtener más información acerca de los centros de IoT Hub, consulte [Creación de una instancia de IoT Hub mediante Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
- El centro de IoT Hub debe tener eventos de mensajes activos en proceso de envío.
- Cree un grupo de consumidores dedicado en el centro de IoT Hub para que el entorno de Time Series Insight los consuma. Cada origen del evento de Time Series Insights tiene que tener su propio grupo de consumidores dedicado que no se comparte con ningún otro consumidor. Si varios lectores consumen eventos desde el mismo grupo de consumidores, es probable que todos los lectores vean errores. Para más información, vea la [Guía para desarrolladores de IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Adición de un nuevo origen del evento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Busque su entorno de Time Series Insights existente. Haga clic en **Todos los recursos** en el menú izquierdo de Azure Portal. Seleccione el entorno de Time Series Insights.

3. En el encabezado **Environment Topology** (Topología del entorno), haga clic en **Orígenes de eventos**.
   ![Orígenes de eventos + Agregar](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Haga clic en **+ Agregar**.

5. Proporcione un **nombre de origen de evento** único para este entorno de Time Series Insights, como **flujo de eventos**.

   ![Rellene los tres primeros parámetros del formulario.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. En **Origen**, seleccione **IoT Hub**.

7. Seleccione la **opción de importación** adecuada. 
   - Elija **Use IoT Hub from available subscriptions** (Usar el centro de IoT Hub de las suscripciones disponibles) cuando ya tenga un centro de eventos existente en una de sus suscripciones. Este es el enfoque más sencillo.
   - Elija **Indicar manualmente la configuración del centro de IoT Hub** cuando el centro de IoT Hub sea externo a sus suscripciones o si quiere elegir opciones avanzadas. 

8. Si ha seleccionado la opción **Use IoT Hub from available subscriptions** (Usar el centro de IoT Hub de las suscripciones disponibles), en la siguiente tabla se explica cada propiedad necesaria:

   ![Detalles del centro de evento y la suscripción](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Propiedad | Descripción |
   | --- | --- |
   | Id. de suscripción | Seleccione la suscripción en la que se creó IoT Hub.
   | Nombre de IoT Hub | Seleccione el nombre del IoT Hub.
   | Nombre de la directiva de IoT Hub | Seleccione la directiva de acceso compartido, que puede encontrarse en la pestaña Configuración de IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **conexión de servicios**.
   | Clave de la directiva de IoT Hub | La clave se rellena previamente.
   | Grupo de consumidores de IoT Hub | El grupo de consumidores para leer eventos desde IoT Hub. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.
   | Formato de serialización de eventos | Por el momento, JSON es la única serialización disponible. Los mensajes de eventos deben estar en este formato, o bien no se podrá leer ningún dato. |
   | Nombre de la propiedad de marca de tiempo | Para determinar este valor, debe comprender el formato de mensaje de los datos del mensaje enviados al centro de IoT Hub. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |

9. Si ha seleccionado la opción **Indicar manualmente la configuración del centro de IoT Hub**, en la tabla siguiente se explica cada propiedad necesaria:

   | Propiedad | Descripción |
   | --- | --- |
   | Id. de suscripción | La suscripción en la que se creó este IoT Hub.
   | Grupos de recursos | El nombre del grupo de recursos en el que se creó este centro de IoT Hub.
   | Nombre de IoT Hub | El nombre de IoT Hub. Cuando creó IoT Hub, también le asignó un nombre específico.
   | Nombre de la directiva de IoT Hub | La directiva de acceso compartido, que puede crearse en la pestaña Configuración de IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **conexión de servicios**.
   | Clave de la directiva de IoT Hub | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres de Service Bus. Escriba la clave principal o secundaria aquí.
   | Grupo de consumidores de IoT Hub | El grupo de consumidores para leer eventos desde IoT Hub. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.
   | Formato de serialización de eventos | Por el momento, JSON es la única serialización disponible. Los mensajes de eventos deben estar en este formato, o bien no se podrá leer ningún dato. |
   | Nombre de la propiedad de marca de tiempo | Para determinar este valor, debe comprender el formato de mensaje de los datos del mensaje enviados al centro de IoT Hub. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |

10. Seleccione **Crear** para agregar el nuevo origen del evento.

   ![Click Create](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Tras la creación del origen de eventos, Time Series Insights iniciará automáticamente la transmisión de datos al entorno.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adición de un grupo de consumidores a IoT Hub
Las aplicaciones usan grupos de consumidores para extraer datos de Azure IoT Hub. Proporcione un grupo de consumidores dedicado, solo para su uso en este entorno de Time Series Insights, para leer datos de manera confiable del centro de IoT Hub.

Para agregar un grupo de consumidores a IoT Hub, siga estos pasos:
1. En Azure Portal, busque y abra el centro de IoT Hub.

2. En el encabezado **Mensajería**, seleccione **Puntos de conexión**. 

   ![Adición de un grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Seleccione el punto de conexión **Eventos** y se abrirá la página **Propiedades**.

4. En el encabezado **Grupos de consumidores**, especifique un nuevo nombre único para el grupo de consumidores. Use este mismo nombre en el entorno de Time Series Insights al crear un nuevo origen de eventos.

5. Seleccione **Guardar** para guardar el nuevo grupo de consumidores.

## <a name="next-steps"></a>Pasos siguientes
- [Defina las directivas de acceso a datos](time-series-insights-data-access.md) para proteger los datos.
- [Envíe eventos](time-series-insights-send-events.md) al origen de eventos.
- Acceso al entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
