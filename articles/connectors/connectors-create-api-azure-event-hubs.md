---
title: "Configurar la supervisión de eventos con Azure Event Hubs para Azure Logic Apps | Microsoft Docs"
description: Supervisar flujos de datos para recibir eventos y enviar eventos para Azure Logic Apps con Azure Event Hubs
services: logic-apps
keywords: "flujo de datos, supervisión de eventos, centros de eventos"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: a7f31c2c17d326d58ede0bb00cdc0f701069ea14
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Supervisar, recibir y enviar eventos con el conector de Event Hubs

Para configurar la supervisión de eventos para que su aplicación lógica pueda detectar eventos, recibir eventos y enviar eventos, conéctese a un [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) desde la aplicación lógica. Obtenga más información acerca de los [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Requisitos

* Debe tener un [espacio de nombres de Event Hubs y un Event Hub](../event-hubs/event-hubs-create.md) en Azure. Obtenga información sobre [cómo crear un espacio de nombres de Event Hubs y un Event Hub](../event-hubs/event-hubs-create.md). 

* Para usar [cualquier conector](https://docs.microsoft.com/azure/connectors/apis-list) en su aplicación lógica, primero debe crearla. Obtenga información sobre [cómo crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Comprobar los permisos del espacio de nombres de Event Hubs y buscar la cadena de conexión

Para que la aplicación lógica tenga acceso a cualquier servicio, tendrá que crear una [*conexión*](./connectors-overview.md) entre la aplicación lógica y el servicio, si todavía no lo ha hecho. Esta conexión autoriza a la aplicación lógica a acceder a los datos.
Para que la aplicación lógica acceda a su Event Hub, debe tener permisos de **Administrador** y la cadena de conexión para el espacio de nombres del Event Hubs.

Para comprobar sus permisos y obtener la cadena de conexión, siga estos pasos.

1.  Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). 

2.  Vaya al *espacio de nombres* de los Event Hubs, no al Event Hub específico. En la hoja del espacio de nombres, haga clic en **Configuración** y elija **Políticas de acceso compartido**. En **Notificaciones**, compruebe que tenga permisos de **Administrador** para ese espacio de nombres.

    ![Administrar los permisos del espacio de nombres del Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Para copiar la cadena de conexión para el espacio de nombres de Event Hubs, elija **RootManageSharedAccessKey**. Al lado de la cadena de conexión de su clave principal, elija el botón Copiar.

    ![Copie la cadena de conexión del espacio de nombres de los Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Para confirmar si la cadena de conexión está asociada con el espacio de nombres de los Event Hubs o con un Event Hub específico, compruebe la cadena de conexión para el parámetro `EntityPath`. Si encuentra este parámetro, la cadena de conexión es para la "entidad" de un Event Hub específico y no es la cadena correcta para utilizar con la aplicación lógica.

4.  Cuando se le soliciten las credenciales después de agregar un desencadenador de los Event Hubs o la acción a la aplicación lógica, puede conectarse al espacio de nombres de los Event Hubs. Asigne un nombre a la conexión, ingrese la cadena de conexión que copió y elija **Crear**.

    ![Ingrese la cadena de conexión para el espacio de nombres de los Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Después de crear la conexión, su nombre debe aparecer en el desencadenador o la acción de los Event Hubs. 
    Ahora puede continuar con el resto de los pasos en la aplicación lógica.

    ![Conexión del espacio de nombres de los Event Hubs creada](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Iniciar el flujo de trabajo cuando el Event Hub recibe eventos nuevos

Un [*desencadenador*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un evento que inicia un flujo de trabajo en la aplicación lógica. Para iniciar un flujo de trabajo cuando se envían nuevos eventos al Event Hub, siga estos pasos para agregar el desencadenador que detecta este evento.

1.  En [Azure Portal](https://portal.azure.com "Azure Portal"), vaya a la aplicación lógica existente o cree una aplicación de lógica en blanco.

2.  En el cuadro de búsqueda del diseñador de aplicaciones lógicas, ingrese `event hubs` para el filtro. Seleccione este desencadenador: **Cuando los eventos estén disponibles en el Event Hub**

    ![Seleccione el desencadenador para cuando el Event Hub reciba eventos nuevos](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Si todavía no tiene una conexión al espacio de nombres de los Event Hubs, se le pedirá que cree ahora esta conexión. Asigne un nombre a la conexión e ingrese la cadena de conexión para el espacio de nombres de los Event Hubs. 
    Si es necesario, obtenga información acerca de [cómo buscar la cadena de conexión](#permissions-connection-string).

    ![Ingrese la cadena de conexión para el espacio de nombres de los Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Después de crear la conexión, aparecerá la configuración del desencadenador **Cuando los eventos estén disponibles en el Event Hub**.

    ![Configuración del desencadenador para cuando el Event Hub reciba eventos nuevos](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Escriba o seleccione el nombre para el Event Hub que desea supervisar. Seleccione la frecuencia y el intervalo con el que desea revisar el Event Hub.

    > [!TIP]
    > Si desea, opcionalmente, seleccionar un grupo de consumidores para leer los eventos, elija **Mostrar opciones avanzadas**. 

    ![Especificar Event Hub o grupo de consumidores](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Ya ha configurado un desencadenador para iniciar un flujo de trabajo para la aplicación lógica. 
    La aplicación lógica comprueba el Event Hub especificado según la programación que haya establecido. 
    Si la aplicación detecta nuevos eventos en el Event Hub, el desencadenador ejecutará otras acciones o desencadenadores en la aplicación lógica.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Enviar eventos al Event Hub desde la aplicación lógica

Una [*acción*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es una tarea realizada por el flujo de trabajo de la aplicación lógica. Después de agregar un desencadenador a la aplicación lógica, puede agregar una acción para llevar a cabo operaciones con los datos generados por ese desencadenador. Para enviar eventos al Event Hub desde la aplicación lógica, siga estos pasos.

1.  En el Diseñador de aplicaciones lógicas, bajo el desencadenador de la aplicación lógica, elija **Nuevo paso** > **Agregar una acción**.

    ![Haga clic en "Nuevo paso" y en "Agregar una acción"](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Ahora puede buscar y seleccionar la acción que desea realizar. 
    Aunque puede seleccionar cualquier acción, en este ejemplo, queremos que la acción de los Event Hubs envíe eventos.

2.  En el cuadro de búsqueda, escriba`event hubs` para el filtro.
Seleccione esta acción: **Enviar evento**

    ![Seleccione la acción "Event Hubs: Enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Ingrese los detalles necesarios para el evento, como el nombre para el Event Hub al que desea enviar el evento. Escriba cualquier otro detalle sobre el evento, como el contenido para ese evento.

    > [!TIP]
    > Para especificar la partición del Event Hub a la cual enviar el evento, elija **Mostrar opciones avanzadas**. 

    ![Escriba el nombre del Event Hub y los detalles opcionales del evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Guarde los cambios.

    ![Guardado de la aplicación lógica](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    Ahora ha configurado una acción para enviar eventos desde la aplicación lógica. 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/eventhubs/). 

## <a name="get-help"></a>Obtención de ayuda

Para formular preguntas, o responderlas, y ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>pasos siguientes

*  [Encuentre otros conectores para Azure Logic apps](./apis-list.md)