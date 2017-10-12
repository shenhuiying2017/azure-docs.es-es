---
title: "Codificación de mensajes X12: Azure Logic Apps | Microsoft Docs"
description: "Valide EDI y convierta mensajes con codificación XML con el codificador de mensajes X12 en Enterprise Integration Pack para Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 29d19364b9a98e351c95f13e68a2e63b9f6439f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Codificación de mensajes X12 para Azure Logic Apps con Enterprise Integration Pack

Con el conector de codificación de mensajes X12, puede validar propiedades EDI y específicas del asociado, convertir mensajes con codificación XML en conjuntos de transacciones EDI en el intercambio y solicitar una confirmación técnica, una funcional o ambas.
Para usar este conector, debe agregarlo a un desencadenador existente en la aplicación lógica.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de codificación de mensajes X12.
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato X12](logic-apps-enterprise-integration-x12.md) ya definido en su cuenta de integración

## <a name="encode-x12-messages"></a>Codificación de mensajes X12

1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).

2. El conector de codificación de mensajes X12 no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, como un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3.  En el cuadro de búsqueda, escriba "x12" para el filtro. Seleccione **X12 - Codificar en mensaje X12 por nombre de contrato** o **X12 - Codificar en mensaje X12 por identidades**.
   
    ![Búsqueda de "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar. 
   
    ![conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

5.  Cuando haya terminado, los detalles de la conexión deberían parecerse a este ejemplo. Para terminar de crear la conexión, elija **Crear**.

    ![creación de la conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    La conexión se ha creado.

    ![detalles de conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 Message por nombre de acuerdo

Si decide codificar mensajes X12 por nombre de contrato, abra la lista **Nombre del contrato X12** y escriba o seleccione el contrato X12 existente. Escriba el mensaje XML para codificar.

![Escriba el nombre del contrato X12 y el mensaje XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Encode X12 Message por identidades

Si elige codificar mensajes X12 por identidades, especifique el identificador y el calificador del remitente, así como el identificador y el calificador del receptor, tal y como están configurados en el contrato X12. Seleccione el mensaje XML para codificar.
   
![Proporcione la identidad del remitente y del receptor, y seleccione el mensaje XML para codificar.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalles de la codificación de X12

El conector de codificación X12 lleva a cabo estas tareas:

* Resolución del acuerdo haciendo coincidir las propiedades de contexto de remitente y receptor.
* Serializa el intercambio EDI, convirtiendo los mensajes codificados en XML en conjuntos de transacciones EDI en el intercambio.
* Aplica segmentos de encabezado y finalizador del conjunto de transacciones.
* Genera un número de control de intercambio, un número de control de grupo y un número de control del conjunto de transacciones para cada intercambio de salida.
* Reemplaza los separadores en los datos de carga útil.
* Valida propiedades EDI y específicas del asociado.
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje.
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones.
  * Validación extendida realizada en los elementos de datos del conjunto de transacciones.
* Solicita una confirmación técnica o funcional (si esta opción está configurada).
  * Se genera una confirmación técnica como resultado de la validación del encabezado. La confirmación técnica informa del estado del procesamiento de un encabezado y finalizador de intercambio por parte del receptor de la dirección.
  * Se genera una confirmación funcional como resultado de la validación del cuerpo. La confirmación funcional informa de cada error encontrado al procesar el documento recibido.

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 

