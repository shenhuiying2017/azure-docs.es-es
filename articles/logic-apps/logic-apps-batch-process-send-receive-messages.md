---
title: "Procesamiento por lotes de mensajes como grupo o colección: Azure Logic Apps | Microsoft Docs"
description: "Envío y recepción de mensajes para el procesamiento por lotes en Logic Apps"
keywords: lote, proceso por lotes
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: de519084a4f172ad984c78727123835eeb9deaef
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Envío, recepción y procesamiento por lotes de mensajes en Logic Apps

Para procesar los mensajes juntos en grupos, puede enviar elementos de datos, o mensajes, a un *lote*y, a continuación, procesar esos elementos como lote. Este enfoque es útil cuando desea asegurarse de que los elementos de datos se agrupan y se procesan de una forma específica. 

Puede crear aplicaciones lógicas que reciben elementos como lote mediante el desencadenador de **lotes**. Puede crear aplicaciones lógicas que envían elementos como lote mediante la acción **Batch**.

Este tema muestra cómo puede compilar una solución de procesamiento por lotes mediante la realización de estas tareas: 

* [Cree una aplicación de lógica que reciba y recopile elementos como lote](#batch-receiver). Esta aplicación lógica "receptora de lotes" especifica los criterios de liberación y nombre de lote que se deben cumplir antes de que la aplicación lógica receptora libere y procese los elementos. 

* [Cree una aplicación lógica que envíe elementos a un lote](#batch-sender). Esta aplicación lógica "remitente de lotes" especifica dónde enviar elementos, que será a una aplicación lógica existente receptora de lotes. También puede especificar una clave única, como un número de cliente, para "particionar" o dividir el lote de destino en subconjuntos basándose en esa clave. De este modo, todos los elementos con esa clave se recopilarán y procesarán juntos. 

## <a name="requirements"></a>Requisitos

Para seguir este ejemplo, necesita estos elementos:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Una cuenta de correo electrónico con cualquier [proveedor de correo electrónico compatible con Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Creación de aplicaciones lógicas que reciben mensajes como lote

Para poder enviar mensajes a un lote, primero debe crear una aplicación lógica "receptora de lotes" con el desencadenador de **lotes**. De este modo, podrá seleccionar dicha aplicación lógica receptora al crear la aplicación lógica remitente. Para la receptora, especifique el nombre de lote, los criterios de liberación y el resto de opciones. 

La aplicación lógica remitente necesita saber dónde enviar los elementos, mientras que la receptora no necesitan saber nada sobre los remitentes.

1. En [Azure Portal](https://portal.azure.com), cree una aplicación lógica con este nombre: "BatchReceiver" 

2. En el Diseñador de Logic Apps, agregue el desencadenador de **lotes** que inicia el flujo de trabajo de la aplicación lógica. En el cuadro de búsqueda, escriba "lotes" como filtro. Seleccione este desencadenador: **Batch: Mensajes de Batch**

   ![Agregar desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Proporcione un nombre para el lote y especifique los criterios para liberar el lote, por ejemplo:

   * **Nombre de lote**: el nombre usado para identificar el lote. En este ejemplo es "TestBatch".
   * **Criterios de liberación**: los criterios de liberación de lotes, que pueden basarse en el número de mensajes, la programación o ambos.
   
     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Número de mensajes**: el número de mensajes que se van a almacenar como lote antes de liberarlo para su procesamiento. En este ejemplo es "5".

     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Programación**: la programación de la liberación de lotes para su procesamiento, que es "cada 5 minutos" en este ejemplo.

     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Agregue otra acción que envíe un correo electrónico cuando se active el desencadenador de lotes. Cada vez que el lote tiene cinco elementos o han pasado 5 minutos, la aplicación lógica envía un correo electrónico.

   1. En el desencadenador de lotes, elija **+ Nuevo paso** > **Agregar una acción**.

   2. En el cuadro de búsqueda, escriba "email" como filtro.
   En función de su proveedor de correo electrónico, seleccione un conector de correo electrónico.
   
      Por ejemplo, si tiene una cuenta profesional o educativa, seleccione el conector de Office 365 Outlook. 
      Si tiene una cuenta de Gmail, seleccione el conector de Gmail.

   3. Seleccione esta acción para el conector: **{*proveedor de correo electrónico*} - Enviar un correo electrónico**

      Por ejemplo: 

      ![Seleccione la acción "Enviar un correo electrónico" para el proveedor de correo electrónico](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Si anteriormente no creó una conexión para su proveedor de correo electrónico, proporcione sus credenciales de correo electrónico para la autenticación cuando se le soliciten. Obtenga más información sobre la [autenticación de sus credenciales de correo electrónico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

6. Establezca las propiedades de la acción que acaba de agregar.

   * En el cuadro **Para**, escriba la dirección de correo electrónico del destinatario. 
   Para realizar pruebas, puede usar su propia dirección de correo electrónico.

   * En el cuadro **Asunto**, cuando aparezca la lista **Contenido dinámico**, seleccione el campo **Nombre de la partición**.

     ![En la lista "Contenido dinámico", seleccione "Nombre de la partición"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     En una sección posterior, puede especificar una clave de partición única que divida el lote de destino en conjuntos lógicos a donde pueda enviar mensajes. 
     Cada conjunto tiene un número único generado por la aplicación lógica remitente. 
     Esta funcionalidad le permite usar un único lote con varios subconjuntos y definir cada subconjunto con el nombre que proporcione.

   * En el cuadro **Cuerpo**, cuando aparezca la lista **Contenido dinámico**, seleccione el campo **Id. del mensaje**.

     ![En "Cuerpo", seleccione "Id. del mensaje"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Como la entrada de la acción de envío de correo electrónico es una matriz, el diseñador agrega automáticamente un bucle **For each** en torno a la acción de **Enviar un correo electrónico**. 
     Este bucle realiza la acción interna en cada elemento del lote. 
     Por lo tanto, con el desencadenador de lotes establecido en cinco elementos, obtendrá cinco mensajes de correo electrónico cada vez que el desencadenador se active.

7.  Ahora que ha creado una aplicación lógica receptora de lotes, guárdela.

    ![Guardado de la aplicación lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Una partición tiene un límite de 5000 mensajes o 80 MB. Si se cumple alguna de estas condiciones, el lote puede liberarse, incluso cuando no se cumple la condición definida por el usuario.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Creación de aplicaciones lógicas que envíen mensajes a un lote

Ahora cree una o más aplicaciones lógicas que envíen elementos al lote definido por la aplicación lógica receptora. Para el remitente, especifique el nombre de la aplicación lógica receptora y el del lote, el contenido del mensaje, y el resto de opciones. Opcionalmente, también puede proporcionar una clave de partición única para dividir el lote en subconjuntos para recopilar elementos con esa clave.

La aplicación lógica remitente necesita saber dónde enviar los elementos, mientras que la receptora no necesitan saber nada sobre los remitentes.

1. Cree otra aplicación lógica con este nombre: "BatchSender".

   1. En el cuadro de búsqueda, escriba "periodicidad" para el filtro. 
   Seleccione este desencadenador: **Programación: Periodicidad**

      ![Agregar el desencadenador "Programación: Periodicidad"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Establezca la frecuencia y el intervalo para ejecutar la aplicación lógica remitente cada minuto.

      ![Establecer la frecuencia y el intervalo del desencadenador Periodicidad](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Agregue un nuevo paso para enviar mensajes a un lote.

   1. En el desencadenador Periodicidad, elija **+ Nuevo paso** > **Agregar una acción**.

   2. En el cuadro de búsqueda, escriba "lotes" como filtro. 

   3. Seleccione esta acción: **Enviar mensajes al lote: Escoja un flujo de trabajo de Logic Apps con el desencadenador de lotes**

      ![Seleccione "Enviar mensajes al lote"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Ahora seleccione la aplicación lógica "BatchReceiver" que creó anteriormente, que ahora aparece como una acción.

      ![Seleccionar aplicación lógica "receptora de lotes"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > La lista también muestra todas las aplicaciones lógicas que tienen desencadenadores de lotes.

3. Establezca las propiedades de lote.

   * **Nombre de lote**: el nombre de lote definido por la aplicación lógica receptora, que es "TestBatch" en este ejemplo y se valida en tiempo de ejecución.

     > [!IMPORTANT]
     > Asegúrese de no cambiar el nombre de lote, que debe coincidir con el nombre de lote especificado por la aplicación lógica receptora.
     > Si cambia el nombre del lote provocará un error en la aplicación lógica remitente.

   * **Contenido del mensaje**: el contenido del mensaje que desea enviar. 
   En este ejemplo, agregue esta expresión que inserta la fecha y hora actuales en el contenido del mensaje que envía al lote:

     1. Cuando aparezca la lista **Contenido dinámico**, elija **Expresión**. 
     2. Escriba la expresión **utcnow()** y elija **Aceptar**. 

        ![En "Contenido del mensaje", elija "Expresión". Escriba "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Ahora debe configurar una partición para el lote. En la acción "BatchReceiver", elija **Mostrar opciones avanzadas**.

   * **Nombre de partición**: una clave de partición única opcional que se utilizará para dividir el lote de destino. En este ejemplo, agregue una expresión que genera un número aleatorio entre uno y cinco.
   
     1. Cuando aparezca la lista **Contenido dinámico**, elija **Expresión**.
     2. Escriba esta expresión: **rand(1,6)**

        ![Establecer una partición para el lote de destino](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Esta función **rand** genera un número comprendido entre uno y cinco. 
        Por tanto, va a dividir este lote en cinco particiones numeradas, que esta expresión establece dinámicamente.

   * **Id. de mensaje**: un identificador de mensaje opcional que es un GUID generado cuando está vacío. 
   En este ejemplo, deje este cuadro en blanco.

5. Guarde la aplicación lógica. La aplicación lógica remitente tiene ahora un aspecto similar al de este ejemplo:

   ![Guardar la aplicación lógica remitente](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Comprobación de las aplicaciones lógicas

Para probar la solución de procesamiento por lotes, deje las aplicaciones lógicas en ejecución durante unos minutos. Pronto, empezará a recibir mensajes de correo electrónico en grupos de cinco, todos con la misma clave de partición.

La aplicación lógica BatchSender se ejecuta cada minuto, genera un número aleatorio entre uno y cinco, y utiliza este número generado como clave de partición para el lote de destino donde se envían los mensajes. Cada vez que el lote tiene cinco elementos con la misma clave de partición, la aplicación lógica BatchReceiver se activa y envía los correos electrónicos para cada mensaje.

> [!IMPORTANT]
> Cuando haya terminado las pruebas, asegúrese de que deshabilita la aplicación lógica BatchSender para detener el envío de mensajes y evitar la sobrecarga de la bandeja de entrada.

## <a name="next-steps"></a>pasos siguientes

* [Generar definiciones de aplicación lógica mediante el uso de JSON](../logic-apps/logic-apps-author-definitions.md)
* [Cree una aplicación sin servidor en Visual Studio con Azure Logic Apps y Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Control de excepciones y registro de errores para aplicaciones lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
