---
title: "Configuración de la supervisión de eventos con Azure Event Hubs para Azure Logic Apps | Microsoft Docs"
description: "Supervisión de flujos de datos para recibir y enviar eventos con aplicaciones lógicas en Azure Event Hubs"
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
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Supervisar, recibir y enviar eventos con el conector de Event Hubs

Para configurar la supervisión de eventos para que su aplicación lógica pueda detectar eventos, recibir eventos y enviar eventos, conéctese a un [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) desde la aplicación lógica. Más información sobre [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) y [Modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>requisitos previos

Para poder usar el conector de Event Hubs, debe tener estos elementos:

* Un [espacio de nombres de Azure Event Hubs y un centro de eventos](../event-hubs/event-hubs-create.md)
* Una [aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Conexión a Azure Event Hubs

Para que la aplicación lógica tenga acceso a algún servicio, tendrá que crear una [*conexión*](./connectors-overview.md) entre la aplicación lógica y el servicio, si todavía no lo ha hecho. Esta conexión autoriza a la aplicación lógica a acceder a los datos. Para que la aplicación lógica acceda a su instancia de Event Hubs, debe tener permisos de administrador y la cadena de conexión para el espacio de nombres de Event Hubs.

1.  Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). 

2.  Vaya al *espacio de nombres* de sus instancias de Event Hubs, no a un centro de eventos específico. En la página del espacio de nombres, en **Configuración**, elija **Directivas de acceso compartido**. En **Notificaciones**, compruebe que tenga permisos de **Administrador** para ese espacio de nombres.

    ![Administrar los permisos del espacio de nombres del Event Hub](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Si más adelante desea escribir manualmente la información de conexión, obtenga la cadena de conexión para el espacio de nombres de sus instancias de Event Hubs. Elija **RootManageSharedAccessKey**. Al lado de la cadena de conexión de su clave principal, elija el botón Copiar. Guarde la cadena de conexión para usarla más adelante.

    ![Copie la cadena de conexión del espacio de nombres de los Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Para confirmar si la cadena de conexión está asociada al espacio de nombres de Event Hubs o a un centro de eventos específico, compruebe la cadena de conexión del parámetro `EntityPath`. Si encuentra este parámetro, la cadena de conexión es para la "entidad" de un Event Hub específico y no es la cadena correcta para utilizar con la aplicación lógica.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Inicio del flujo de trabajo cuando Event Hub recibe eventos nuevos

Un [*desencadenador*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un evento que inicia un flujo de trabajo en la aplicación lógica. Para iniciar un flujo de trabajo cuando se envían nuevos eventos al Event Hub, siga estos pasos para agregar el desencadenador que detecta este evento.

1. En [Azure Portal](https://portal.azure.com "Azure Portal"), vaya a la aplicación lógica existente o cree una aplicación de lógica en blanco.

2. En el Diseñador de aplicaciones lógicas, escriba "event hubs" en el cuadro de búsqueda como filtro. Seleccione este desencadenador: **Cuando los eventos estén disponibles en el Event Hub**

   ![Seleccione el desencadenador para cuando el Event Hub reciba eventos nuevos](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Si todavía no tiene una conexión al espacio de nombres de los Event Hubs, se le pedirá que cree ahora esta conexión. Asigne un nombre a la conexión y seleccione el espacio de nombres de Event Hubs que desea usar.

      ![Creación de una conexión de Event Hubs](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      O bien, para escribir de forma manual la cadena de conexión, elija **Especificar la información de conexión manualmente**. 
      Obtenga información acerca de [cómo buscar la cadena de conexión](#permissions-connection-string).

   2. Ahora seleccione la directiva de Event Hubs que desea usar y elija **Crear**.

      ![Creación de la cadena de conexión de Event Hubs, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Seleccione el centro de eventos que se desea supervisar y configure el intervalo y la frecuencia que determinan cuándo comprobarlo.

    ![Especificar Event Hub o grupo de consumidores](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > Si desea, opcionalmente, seleccionar un grupo de consumidores para leer los eventos, elija **Mostrar opciones avanzadas**.

4. Guarde la aplicación lógica. En la barra de herramientas del diseñador, haga clic en **Guardar**.

Ahora, cuando la aplicación lógica comprueba el centro de eventos seleccionado y encuentra un evento nuevo, el desencadenador ejecuta las acciones de la aplicación lógica para el evento encontrado.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Enviar eventos al Event Hub desde la aplicación lógica

Una [*acción*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es una tarea realizada por el flujo de trabajo de la aplicación lógica. Después de agregar un desencadenador a la aplicación lógica, puede agregar una acción para llevar a cabo operaciones con los datos generados por ese desencadenador. Para enviar eventos al Event Hub desde la aplicación lógica, siga estos pasos.

1. En el Diseñador de aplicaciones lógicas, bajo el desencadenador, elija **Nuevo paso** > **Agregar una acción**.

2. En el cuadro de búsqueda, escriba "event hubs" como filtro.
Seleccione la acción **Event Hubs: Enviar evento**

   ![Seleccione "Event Hubs: Enviar evento"](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Seleccione el centro de eventos donde enviar el evento. A continuación, escriba el contenido del evento y otros detalles.

   ![Seleccione el nombre del centro de eventos y proporcione su contenido](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Guarde la aplicación lógica.

Ahora ha configurado una acción que envía eventos desde la aplicación lógica. 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Para obtener más información acerca de los desencadenadores y las acciones definidos por el archivo Swagger y los límites, repase los [detalles del conector](/connectors/eventhubs/).

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>pasos siguientes

* Más información sobre [otros conectores para Azure Logic Apps](../connectors/apis-list.md)