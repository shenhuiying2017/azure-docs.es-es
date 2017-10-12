---
title: "Descodificación de mensajes AS2: Azure Logic Apps | Microsoft Docs"
description: Uso del descodificador AS2 en Enterprise Integration Pack para Azure Logic Apps
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
ms.author: LADocs; padmavc
ms.openlocfilehash: a7920b2509fe368c6f7d55e17fe0bf0020c4562c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificación de mensajes AS2 para Azure Logic Apps con Enterprise Integration Pack 

Utilice el conector de descodificación de mensajes AS2 para garantizar la seguridad y confiabilidad al transmitir mensajes. Este conector proporciona firma digital, descifrado y confirmaciones a través de las notificaciones de disposición de mensajes (MDN).

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de descodificación de mensajes AS2.
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato AS2](logic-apps-enterprise-integration-as2.md) ya definido en la cuenta de integración.

## <a name="decode-as2-messages"></a>Descodificación de mensajes AS2

1. [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

2. El conector de descodificación de mensajes AS2 no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, por ejemplo, un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3.  En el cuadro de búsqueda, escriba "AS2" para el filtro. Seleccione **AS2 – Decode AS2 Message** (AS2: descodificación de mensajes AS2).
   
    ![Búsqueda de "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar.
   
    ![Crear conexión de integración](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

5.  Cuando haya terminado, los detalles de la conexión deberían parecerse a este ejemplo. Para terminar de crear la conexión, elija **Crear**.

    ![detalles de la conexión de integración](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Una vez creada la conexión, como se muestra en este ejemplo, seleccione **Cuerpo** y **Encabezados** de las salidas de la solicitud.
   
    ![conexión de integración creada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Por ejemplo:

    ![Seleccione el cuerpo y los encabezados de las salidas de la solicitud](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Detalles de descodificador de AS2

El conector de descodificación AS2 lleva a cabo estas tareas: 

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

## <a name="try-this-sample"></a>Ejemplo para probar

Para intentar implementar una aplicación lógica totalmente operativa y conocer el escenario de ejemplo de AS2, consulte el artículo sobre [escenario y plantilla de aplicaciones lógicas de AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

