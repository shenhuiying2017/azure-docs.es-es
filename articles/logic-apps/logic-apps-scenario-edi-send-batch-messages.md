---
title: "Procesamiento por lotes de mensajes de EDI como grupo o colección - Azure Logic Apps | Microsoft Docs"
description: "Envíe mensajes de EDI para el procesamiento por lotes en Logic Apps."
keywords: batch, batch process, batch encode
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Envío de mensajes X12 en lotes a asociados comerciales

En escenarios de negocio a negocio (B2B), los asociados a menudo intercambian mensajes en grupos o lotes. Para enviar mensajes en grupos o lotes a asociados comerciales, puede crear un lote con varios elementos y, a continuación, usar la acción en lotes de X12 para procesar esos elementos como un lote.


El procesamiento por lotes para mensajes X12, como otros mensajes, usa un desencadenador y una acción de lote. También para X12, el lote se somete a un paso de codificación X12 antes de llegar al asociado o a cualquier otro destino. Para obtener más información sobre el desencadenador y la acción de lote, vea [Envío, recepción y procesamiento por lotes de mensajes en Logic Apps](logic-apps-batch-process-send-receive-messages.md).

En este tema se muestra la forma de procesar mensajes X12 como un lote mediante la realización de estas tareas:
* [Cree una aplicación lógica que reciba elementos y cree un lote](#receiver). Esta aplicación lógica "receptora" lleva a cabo estas acciones:
 
   * Especifica el nombre del lote y los criterios de liberación que se deben cumplir a fin de publicar los elementos como un lote.

   * Procesa o codifica los elementos del lote utilizando el contrato X12 o las identidades del asociado.

* [Cree una aplicación lógica que envíe elementos a un lote](#sender). Esta aplicación lógica "remitente" especifica dónde enviar elementos para el procesamiento de lotes, que será una aplicación lógica existente receptora.


## <a name="prerequisites"></a>Requisitos previos

Para seguir este ejemplo, necesita estos elementos:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure

* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que haya definido en su cuenta de integración. Asegúrese de que cada asociado utilice el calificador X12 (Standard Carrier Alpha Code) en las propiedades del asociado como identidad empresarial.

* Un [contrato X12](logic-apps-enterprise-integration-x12.md) ya definido en su cuenta de integración

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Creación de una aplicación lógica que reciba mensajes X12 y cree un lote

Para poder enviar mensajes a un lote, primero debe crear una aplicación lógica "receptora" con el desencadenador de **lotes**. De este modo, podrá seleccionar dicha aplicación lógica receptora al crear la aplicación lógica remitente. Para la receptora, especifique el nombre de lote, los criterios de liberación, el contrato X12 y el resto de opciones. 


1. En [Azure Portal](https://portal.azure.com), cree una aplicación lógica con este nombre: "BatchX12Messages"

2. En el Diseñador de Logic Apps, agregue el desencadenador de **lotes** que inicia el flujo de trabajo de la aplicación lógica. En el cuadro de búsqueda, escriba "lotes" como filtro. Seleccione este desencadenador: **Batch: Mensajes de Batch**

   ![Agregar desencadenador de lotes](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Proporcione un nombre para el lote y especifique los criterios para liberar el lote, por ejemplo:

   * **Nombre de lote**: el nombre usado para identificar el lote. En este ejemplo es "TestBatch".

   * **Criterios de liberación**: los criterios de liberación de lotes, que pueden basarse en el número de mensajes, la programación o ambos.
   
     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Número de mensajes**: el número de mensajes que se van a almacenar como lote antes de liberarlo para su procesamiento. En este ejemplo es "5".

     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Programación**: la programación de la liberación de lotes para su procesamiento, que es "cada 10 minutos" en este ejemplo.

     ![Proporcionar detalles del desencadenador de lotes](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Agregue otra acción que codifique los mensajes agrupados o por lotes y que cree un mensaje por lotes X12. 

   a. Elija **+Nuevo paso** > **Agregar una acción**.

   b. En el cuadro de búsqueda, escriba "lote X12" como filtro y seleccione una acción para **X12 - Batch Encode** (X12 - Codificación por lotes). Como ocurre con el conector de codificación X12, existen múltiples variaciones para la acción de codificación por lotes. Puede seleccionar cualquiera de ellas.

   ![Seleccione la acción de codificación por lotes X12.](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Establezca las propiedades de la acción que acaba de agregar.

   * En **Nombre del contrato X12** , seleccione el acuerdo en la lista desplegable. Si la lista está vacía, asegúrese de que ha creado una conexión a su cuenta de integración.

   * En el cuadro **BatchName**, seleccione el campo **Nombre de lote** en la lista de contenido dinámico.
   
   * En el cuadro **PartitionName**, seleccione el campo **Nombre de partición** en la lista de contenido dinámico.

   * En el cuadro **Items**, seleccione los **Elementos por lotes** en la lista de contenido dinámico.

   ![Detalles de la acción de codificación por lotes](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Con fines de prueba, agregue una acción HTTP para enviar el mensaje por lotes al [servicio Request Bin](https://requestbin.fullcontact.com/). 

   1. En el cuadro de búsqueda, escriba "HTTP" como filtro. Seleccione esta acción: **HTTP - HTTP**.
    
      ![Selección de la acción de HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. En la lista **Método**, seleccione **POST**. En el cuadro **Uri**, genere un URI para Request Bin y escriba esa URI. En el cuadro **Cuerpo**, cuando se abre la lista dinámica, seleccione el campo **Cuerpo** bajo la sección **Batch permite codificar por nombre de contrato**. Si no ve **Cuerpo**, elija **Ver más** junto a **Batch permite codificar por nombre de contrato**.

      ![Especificación de los detalles de la acción HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Ahora que ha creado una aplicación lógica receptora, guárdela.

    ![Guardado de la aplicación lógica](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Una partición tiene un límite de 5000 mensajes o 80 MB. Si se cumple alguna de estas condiciones, el lote puede liberarse, incluso cuando no se cumple la condición definida por el usuario.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Creación de una aplicación lógica que envíe mensajes X12 a un lote

Ahora cree una o más aplicaciones lógicas que envíen elementos al lote definido por la aplicación lógica receptora. Para el remitente, especifique el nombre de la aplicación lógica receptora y el del lote, el contenido del mensaje, y el resto de opciones. Opcionalmente, también puede proporcionar una clave de partición única para dividir el lote en subconjuntos para recopilar elementos con esa clave.

La aplicación lógica remitente necesita saber dónde enviar los elementos, mientras que la receptora no necesita saber nada sobre los remitentes.


1. Cree otra aplicación lógica con el nombre "X12MessageSender". Agregue este desencadenador a la aplicación lógica: **Request / Response - Request** 
   
   ![Adición del desencadenador Request](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Agregue un nuevo paso para enviar mensajes a un lote.

   1. Elija **+Nuevo paso** > **Agregar una acción**.

   2. En el cuadro de búsqueda, escriba "lotes" como filtro. 

3. Seleccione esta acción: **Enviar mensajes al lote: Escoja un flujo de trabajo de Logic Apps con el desencadenador de lotes**

   ![Seleccione "Enviar mensajes al lote"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Ahora seleccione la aplicación lógica "BatchX12Messages" que creó anteriormente, que ahora aparece como una acción.

   ![Seleccionar aplicación lógica "receptora de lotes"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > La lista también muestra todas las aplicaciones lógicas que tienen desencadenadores de lotes.

5. Establezca las propiedades de lote.

   * **Nombre de lote**: el nombre de lote definido por la aplicación lógica receptora, que es "TestBatch" en este ejemplo y se valida en tiempo de ejecución.

     > [!IMPORTANT]
     > Asegúrese de no cambiar el nombre de lote, que debe coincidir con el nombre de lote especificado por la aplicación lógica receptora.
     > Si cambia el nombre del lote provocará un error en la aplicación lógica remitente.

   * **Contenido del mensaje**: el contenido del mensaje que desea enviar al lote
   
   ![Establecimiento de las propiedades de lote](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Guarde la aplicación lógica. La aplicación lógica remitente tiene ahora un aspecto similar al de este ejemplo:

   ![Guardar la aplicación lógica remitente](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Comprobación de las aplicaciones lógicas

Para probar la solución de procesamiento por lotes, publique mensajes X12 en la aplicación lógica remitente desde [Postman](https://www.getpostman.com/postman) o una herramienta similar. Pronto debería empezar a recibir mensajes X12, ya sea como un lote de cinco elementos o cada 10 minutos, en su Request Bin: todos con la misma clave de partición.

## <a name="next-steps"></a>Pasos siguientes

* [Envío, recepción y procesamiento por lotes de mensajes en Logic Apps](logic-apps-batch-process-send-receive-messages.md) 
* [Cree una aplicación sin servidor en Visual Studio con Azure Logic Apps y Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Control de excepciones y registro de errores para aplicaciones lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
