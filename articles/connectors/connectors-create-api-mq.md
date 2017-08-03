 
---
title: Uso del conector de MQ en Azure Logic Apps | Microsoft Docs
description: "Conexión a un servidor local o de Azure MQ desde flujo de trabajo de la aplicación lógica para examinar, recibir y enviar mensajes a WebSphere MQ"
services: logic-apps
author: valthom
manager: anneta
documentationcenter: 
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/01/2017
ms.author: valthom; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: d2eca57f8bd265436437f9340a23fdcfe8bc9057
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017

---

# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Conexión a un servidor IBM MQ desde las aplicaciones lógicas mediante el conector de MQ 

Microsoft Connector for MQ envía y recupera mensajes almacenados en un servidor MQ local o en Azure. Este conector incluye un cliente de Microsoft MQ que se comunica con un servidor IBM MQ remoto a través de una red TCP/IP. Este documento es una guía de inicio para usar el conector de MQ. Se recomienda empezar por explorar un mensaje de una cola y, después, intentar las demás acciones.    

El conector de MQ incluye las siguientes acciones. No hay desencadenadores.

-   Examinar un único mensaje sin eliminarlo del servidor IBM MQ
-   Examinar un lote de mensajes sin eliminarlos del servidor IBM MQ
-   Recibir un único mensaje y eliminarlo del servidor IBM MQ
-   Recibir un lote de mensajes y eliminarlos del servidor IBM MQ
-   Enviar un único mensaje al servidor IBM MQ 

## <a name="prerequisites"></a>Requisitos previos

* Si usa un servidor local de MQ, [instale la puerta de enlace de datos local](../logic-apps/logic-apps-gateway-install.md) en un servidor de la red. Si el servidor MQ está públicamente disponible o disponible en Azure, no se necesita ni se usa la puerta de enlace de datos.

    > [!NOTE]
    > El servidor donde está instalada la puerta de enlace de datos local también debe tener .Net Framework 4.6 instalado para que el conector de MQ funcione.

* Cree el recurso de Azure para la puerta de enlace de datos local: [Configuración de la conexión de la puerta de enlace de datos](../logic-apps/logic-apps-gateway-connection.md).

* Versiones oficialmente compatibles con IBM WebSphere MQ:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Creación de una aplicación lógica

1. En el **Panel de inicio de Azure**, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**. 
2. Rellene los campos **Nombre**, como MQTestApp, **Suscripción**, **Grupo de recursos** y **Ubicación** (use la ubicación donde esté configurada la conexión de la puerta de enlace de datos local). Seleccione **Anclar al panel** y **Crear**.  
![Creación de la aplicación lógica](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Incorporación de un desencadenador

> [!NOTE]
> El conector de MQ no tiene ningún desencadenador. Debe usar otro desencadenador para iniciar la aplicación lógica, como **Periodicidad**. 

1. Cuando se abra el **Diseñador de aplicaciones de la lógicas**, seleccione **Periodicidad** de la lista de desencadenadores comunes.
2. En el desencadenador Periodicidad, seleccione **Editar**. 
3. Establezca la **Frecuencia** en **Día**, con un **intervalo** de **7**. 

## <a name="browse-a-single-message"></a>Examen de un único mensaje
1. Seleccione **+ Nuevo paso** y **Agregar una acción**.
2. En el cuadro de búsqueda, escriba `mq` y seleccione **MQ - Browse message** (MQ: examinar mensaje).  
![Examen de un mensaje](media/connectors-create-api-mq/Browse_message.png)

3. Si no hay conexión de MQ existente, créela:  

    1. Seleccione **Conectarse mediante puerta de enlace de datos local** y especifique las propiedades del servidor MQ.  
    Para **Servidor**, especifique el nombre del servidor MQ, o escriba la dirección IP seguida de dos puntos y el número del puerto. 
    2. La lista desplegable de **puertas de enlace** muestra las conexiones de puerta de enlace existentes configuradas. Seleccione la puerta de enlace.
    3. Cuando haya terminado, seleccione **Crear**. La conexión se parecerá a la siguiente:   
    ![Propiedades de la conexión](media/connectors-create-api-mq/Connection_Properties.png)

4. En las propiedades de acción, puede:  

    * Use la propiedad **Queue** para acceder a un nombre de cola distinto al que se define en la conexión
    * Usar las propiedades **MessageId**, **CorrelationId**, **GroupId** y otras para buscar un mensaje en función de las distintas propiedades de los mensajes de MQ
    * Establecer **IncludeInfo** en **True** para incluir información adicional al mensaje de salida. O establecerlo en **False** para no incluir información adicional al mensaje de salida.
    * Escribir un valor para **Timeout** para determinar cuánto tiempo esperar a que llegue un mensaje a una cola vacía. Si no se especifica nada, se recupera el primer mensaje de la cola y no hay tiempo de espera para que aparezca un mensaje.  
    ![Propiedades de Browse message](media/connectors-create-api-mq/Browse_message_Props.png)

5. Haga clic en **Guardar** para guardar los cambios y en **Ejecutar** para ejecutar la aplicación lógica:  
![Guardar y ejecutar](media/connectors-create-api-mq/Save_Run.png)

6. Después de unos segundos, los pasos de la ejecución aparecerán y podrá observar la salida. Seleccione la marca de verificación verde para ver los detalles de cada paso. Seleccione **Mostrar salidas sin procesar** para ver más detalles de los datos de salida.  
![Salida de Browse message](media/connectors-create-api-mq/Browse_message_output.png)  

    Salida sin procesar:  
    ![Salida sin procesar de Browse message](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Cuando la opción **IncludeInfo** se establece en true, aparece el resultado siguiente:  
![IncludeInfo de Browse message](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Examen de varios mensajes
La acción **Browse messages** incluye la opción **BatchSize** para indicar cuántos mensajes se deben devolver de la cola.  Si **BatchSize** se deja en blanco, se devuelven todos los mensajes. El resultado devuelto es una matriz de mensajes.

1. Al agregar la acción **Browse messages**, la primera conexión configurada se selecciona de manera predeterminada. Seleccione **Cambiar conexión** para crear una conexión nueva o seleccione una diferente.

2. La salida de Browse messages muestra:  
![Salida de Browse message](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Recepción de un único mensaje
La acción **Receive message** tiene las mismas entradas y salidas que la acción **Browse message**. Cuando se usa **Receive message**, el mensaje se elimina de la cola.

## <a name="receive-multiple-messages"></a>Recepción de varios mensajes
La acción **Receive messages** tiene las mismas entradas y salidas que la acción **Browse messages**. Cuando se usa **Receive messages**, los mensajes se eliminan de la cola.

Si no hay mensajes en la cola al realizar la acción browse o receive, el paso produce un error con la siguiente salida:  
![Error de ausencia de mensajes en MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Envío de un mensaje
1. Al agregar la acción **Send message**, la primera conexión configurada se selecciona de manera predeterminada. Seleccione **Cambiar conexión** para crear una conexión nueva o seleccione una diferente. Los valores válidos para **Message Types** son **Datagram**, **Reply** o **Request**.  
![Propiedades de Send message](media/connectors-create-api-mq/Send_Msg_Props.png)

2. La salida de Send message tendrá un aspecto similar al siguiente:  
![Salida de Send message](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/mq/).

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

