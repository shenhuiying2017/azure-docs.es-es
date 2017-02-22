---
title: "Codificación de mensajes AS2 en Azure Logic Apps | Microsoft Docs"
description: Uso del codificador AS2 incluido con Enterprise Integration Pack y Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Introducción a Encode AS2 Message
Conéctese a Encode AS2 Message para garantizar la seguridad y confiabilidad al transmitir mensajes. Ofrece firma digital, cifrado y confirmaciones mediante las notificaciones de disposición del mensaje (MDN), lo que también conlleva una compatibilidad con el no rechazo.

## <a name="prereqs"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Se requiere una cuenta de integración para usar el conector Encode AS2 Message. Consulte cómo crear una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), [asociados](logic-apps-enterprise-integration-partners.md) y un [contrato de AS2](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>Codificación de mensajes AS2
1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "AS2" en el cuadro de búsqueda.  Seleccione AS2 – Encode AS2 Message (AS2: Encode AS2 Message):
   
    ![buscar AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión:
   
    ![crear la conexión con la cuenta de integración](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias:
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
      Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:
   
      ![conexión de integración establecida](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. Seleccione **Crear**.
6. Observe que la conexión se ha creado en el portal.  Especifique los identificadores AS2-From y AS2-To (como se hayan configurado en el contrato), y los detalles del cuerpo (la carga útil del mensaje):
   
    ![especificar los campos obligatorios](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Detalles de codificador de AS2
El conector Encode AS2 hace lo siguiente: 

* Aplica los encabezados AS2/HTTP.
* Firma los mensajes salientes (en caso de haberse configurado).
* Cifra los mensajes salientes (en caso de haberse configurado).
* Comprime el mensaje (en caso de haberse configurado).

## <a name="try-it-yourself"></a>Hágalo usted mismo
Pruébelo. Use el [escenario y la plantilla de aplicaciones lógicas de AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implementar una aplicación lógica totalmente operativa.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


