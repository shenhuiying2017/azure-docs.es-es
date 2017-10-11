---
title: "Codificación de mensajes AS2: Azure Logic Apps | Microsoft Docs"
description: Uso del codificador AS2 en Enterprise Integration Pack para Azure Logic Apps
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
ms.author: LADocs; padmavc
ms.openlocfilehash: 7889bf9e4e02143b6bb4c797531afa54f8647ce5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Codificación de mensajes AS2 para Azure Logic Apps con Enterprise Integration Pack

Utilice el conector de codificación de mensajes AS2 para garantizar la seguridad y confiabilidad al transmitir mensajes. Este conector ofrece firma digital, cifrado y confirmaciones mediante las notificaciones de disposición del mensaje (MDN), lo que también conlleva compatibilidad con la recepción sin rechazo.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de codificación de mensajes AS2.
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato AS2](logic-apps-enterprise-integration-as2.md) ya definido en la cuenta de integración.

## <a name="encode-as2-messages"></a>Codificación de mensajes AS2

1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).

2. El conector de codificación de mensajes AS2 no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, por ejemplo, un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3.  En el cuadro de búsqueda, escriba "AS2" para el filtro. Seleccione **AS2 – Encode AS2 Message** (AS2: codificación de mensajes AS2).
   
    ![Búsqueda de "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar. 
   
    ![crear la conexión con la cuenta de integración](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

5.  Cuando haya terminado, los detalles de la conexión deberían parecerse a este ejemplo. Para terminar de crear la conexión, elija **Crear**.
   
    ![detalles de la conexión de integración](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Una vez creada la conexión, como se muestra en este ejemplo, proporcione los detalles de los identificadores **AS2-From**, **AS2-To** como esté configurado en el contrato y de **Cuerpo**, que es la carga útil del mensaje.
   
    ![especificar los campos obligatorios](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Detalles de codificador de AS2

El conector de codificación AS2 lleva a cabo estas tareas: 

* Aplica los encabezados AS2/HTTP.
* Firma los mensajes salientes (en caso de haberse configurado).
* Cifra los mensajes salientes (en caso de haberse configurado).
* Comprime el mensaje (en caso de haberse configurado).

## <a name="try-this-sample"></a>Ejemplo para probar

Para intentar implementar una aplicación lógica totalmente operativa y conocer el escenario de ejemplo de AS2, consulte el artículo sobre [escenario y plantilla de aplicaciones lógicas de AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 

