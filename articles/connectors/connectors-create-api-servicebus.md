---
title: "Configuración de la mensajería con Azure Service Bus para Azure Logic Apps | Microsoft Docs"
description: "Envío y recepción de mensajes con aplicaciones lógicas mediante Azure Service Bus"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Envío y recepción de mensajes con el conector de Azure Service Bus

Para enviar y recibir mensajes con una aplicación lógica, conéctese a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Puede realizar acciones como enviar a una cola, enviar a un tema, recibir de una cola o recibir de una suscripción. Más información sobre [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) y [Modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>requisitos previos

Para poder usar el conector de Service Bus, debe tener estos elementos, que deben existir en la misma suscripción de Azure de modo que sean visibles entre sí:

* Un [espacio de nombres de Service Bus y una entidad de mensajería, como una cola](../service-bus-messaging/service-bus-create-namespace-portal.md)
* Una [aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Conexión a Azure Service Bus

Para que la aplicación lógica tenga acceso a algún servicio, tendrá que crear una [*conexión*](./connectors-overview.md) entre la aplicación lógica y el servicio, si todavía no lo ha hecho. Esta conexión autoriza a la aplicación lógica a acceder a los datos. Para que la aplicación lógica tenga acceso a su cuenta de Service Bus, compruebe sus permisos.

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Vaya a su *espacio de nombres* de Service Bus, no a una entidad de mensajería específica. En la página del espacio de nombres, en **Configuración**, elija **Directivas de acceso compartido**. En **Notificaciones**, compruebe que tenga permisos de **Administrador** para ese espacio de nombres.

   ![Administración de permisos para un espacio de nombres de Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Si más adelante desea escribir manualmente su información de conexión, obtenga la cadena de conexión para su espacio de nombres de Service Bus. Elija **RootManageSharedAccessKey**. Al lado de la cadena de conexión de su clave principal, elija el botón Copiar. Guarde la cadena de conexión para usarla más adelante.

   ![Copia de la cadena de conexión del espacio de nombres de Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar si la cadena de conexión está asociada al espacio de nombres de Service Bus o a una entidad específica, compruebe la cadena de conexión del parámetro `EntityPath`. Si encuentra este parámetro, la cadena de conexión es para una entidad específica y no es la correcta para utilizar con la aplicación lógica.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Inicio del flujo de trabajo cuando Service Bus recibe mensajes nuevos

Un [*desencadenador*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un evento que inicia un flujo de trabajo en la aplicación lógica. Para iniciar un flujo de trabajo cuando se envían mensajes nuevos a Service Bus, siga estos pasos para agregar el desencadenador que detecta estos mensajes.

1. En [Azure Portal](https://portal.azure.com "Azure Portal"), vaya a la aplicación lógica existente o cree una aplicación de lógica en blanco.

2. En el Diseñador de aplicaciones lógicas, escriba "service bus" en el cuadro de búsqueda como filtro. Seleccione el conector de **Service Bus**. 

   ![Selección del conector de Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Seleccione el desencadenador que desea usar. Por ejemplo, para ejecutar una aplicación lógica cuando un nuevo elemento se envía a una cola de Service Bus, seleccione este desencadenador **Service Bus - When a message is received in a queue** (Service Bus: cuando se recibe un mensaje en una cola).

   ![Selección de un desencadenador de Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. Si todavía no tiene una conexión al espacio de nombres de Service Bus, se le pedirá que cree ahora esta conexión. Asigne un nombre a la conexión y seleccione el espacio de nombres de Service Bus que desea usar.

      ![Creación de una conexión de Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      O bien, para escribir de forma manual la cadena de conexión, elija **Especificar la información de conexión manualmente**. 
      Obtenga información acerca de [cómo buscar la cadena de conexión](#permissions-connection-string).

   2. Ahora, seleccione la directiva de Service Bus que desea usar y elija **Crear**.

      ![Creación de una conexión de Service Bus, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Seleccione la cola de Service Bus que desea usar y configure el intervalo y la frecuencia que determinan cuándo comprobarla.

   ![Selección de la cola de Service Bus, configuración del intervalo de sondeo](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. Guarde la aplicación lógica. En la barra de herramientas del diseñador, haga clic en **Guardar**.

Ahora, cuando la aplicación lógica comprueba la cola seleccionada y encuentra un mensaje nuevo, el desencadenador ejecuta las acciones de la aplicación lógica para el mensaje encontrado.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Envío de mensajes desde la aplicación lógica al Service Bus

Una [*acción*](../logic-apps/logic-apps-overview.md#logic-app-concepts) es una tarea realizada por el flujo de trabajo de la aplicación lógica. Después de agregar un desencadenador a la aplicación lógica, puede agregar una acción para llevar a cabo operaciones con los datos generados por ese desencadenador. Para enviar un mensaje a la entidad de mensajería de Service Bus desde la aplicación lógica, siga estos pasos.

1. En el Diseñador de aplicaciones lógicas, bajo el desencadenador, elija **+ Nuevo paso** > **Agregar una acción**.

2. En el cuadro de búsqueda, escriba "service bus" como filtro. Seleccione este conector: **Service Bus**.

   ![Selección del conector de Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Seleccione esta acción: **Service Bus: Enviar mensaje**

   ![Selección de "Service Bus: Enviar mensaje"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Seleccione la entidad de mensajería, que es el nombre de la cola o del tema a donde enviar el mensaje. A continuación, escriba el contenido del mensaje y otros detalles.

   ![Selección de la entidad de mensajería y detalles del mensaje](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Guarde la aplicación lógica. 

Ahora ha configurado una acción que envía mensajes desde la aplicación lógica. 

## <a name="connector-specific-details"></a>Detalles específicos del conector

Para obtener más información acerca de los desencadenadores y las acciones definidos por el archivo Swagger y los límites, repase los [detalles del conector](/connectors/servicebus/).

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>pasos siguientes

* Más información sobre [otros conectores para Azure Logic Apps](../connectors/apis-list.md)