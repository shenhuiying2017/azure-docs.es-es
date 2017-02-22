---
title: "Descodificación de mensajes AS2 en Azure Logic Apps | Microsoft Docs"
description: Uso del descodificador AS2 incluido con Enterprise Integration Pack y Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>Introducción a la descodificación del mensaje AS2 en las aplicaciones lógicas
Conéctese a Decode AS2 Message para establecer seguridad y confiabilidad al transmitir mensajes. Proporciona firma digital, descifrado y confirmaciones a través de las notificaciones de disposición de mensajes (MDN).

## <a name="prereqs"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Se requiere una cuenta de integración para usar el conector de Decode AS2 Message. Consulte [cómo crear una cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), [asociados](logic-apps-enterprise-integration-partners.md) y un [contrato de AS2](../logic-apps/logic-apps-enterprise-integration-as2.md).

## <a name="decode-as2-messages"></a>Descodificación de mensajes AS2
1. [Cree una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "AS2" en el cuadro de búsqueda.  Seleccione AS2 – Decode AS2 Message:
   
    ![Buscar AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión:
   
    ![Crear conexión de integración](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias:
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
      Una vez completados, los detalles de la conexión presentan un aspecto similar al siguiente.
   
      ![conexión de integración](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. Seleccione **Crear**.
6. Observe que la conexión se ha creado en el portal.  Ahora, continúe con el resto de los pasos en la aplicación lógica:
   
    ![conexión de integración creada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. Seleccione el cuerpo y los encabezados de las salidas de la solicitud:
   
    ![especificar los campos obligatorios](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Detalles de descodificador de AS2
El conector Decode AS2 hace lo siguiente: 

* Procesa encabezados AS2/HTTP.
* Comprueba la firma (si la opción está configurada).
* Descifra los mensajes (si la opción está configurada).
* Descomprime el mensaje (si la opción está configurada).
* Reconcilia un MDN recibido con el mensaje de salida original.
* Actualiza y correlaciona registros en la base de datos sin repudio.
* Escribe registros para los informes de estado de AS2.
* El contenido de la carga útil de salida está codificado en base64.
* Determina si se requiere un MDN y si este debe ser sincrónico o asincrónico en función de la configuración del acuerdo AS2.
* Genera un MDN sincrónico o asincrónico (en función de las configuraciones del acuerdo).
* Establece las propiedades y los token de correlación en el MDN.

## <a name="try-it-yourself"></a>Hágalo usted mismo
Pruébelo. Use el [escenario y la plantilla de aplicaciones lógicas de AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implementar una aplicación lógica totalmente operativa.

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


