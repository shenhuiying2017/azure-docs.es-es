---
title: "Codificación de mensajes EDIFACT: Azure Logic Apps | Microsoft Docs"
description: Valide EDI y genere XML con el codificador de mensajes EDIFACT en Enterprise Integration Pack para Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: b8d577326d23ec45cb4a9ec0e450ebf7afd945f3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Codificación de mensajes EDIFACT para Azure Logic Apps con Enterprise Integration Pack

Con el conector de codificación de mensajes EDIFACT puede validar EDI y propiedades específicas del asociado, generar un documento XML para cada conjunto de transacciones y solicitar la confirmación técnica, la funcional o ambas.
Para usar este conector, debe agregarlo a un desencadenador existente en la aplicación lógica.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de codificación de mensajes EDIFACT. 
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) ya definido en la cuenta de integración.

## <a name="encode-edifact-messages"></a>Codificación de mensajes EDIFACT

1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).

2. El conector de codificación de mensajes EDIFACT no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, por ejemplo, un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3.  En el cuadro de búsqueda, escriba "EDIFACT" para el filtro. Seleccione **Encode EDIFACT Message by agreement name** (Codificación de mensajes EDIFACT por nombre de contrato) o **Encode to EDIFACT Message by identities** (Codificación de mensajes EDIFACT por identidad).
   
    ![buscar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar.

    ![crear la conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

5.  Cuando haya terminado, los detalles de la conexión deberían parecerse a este ejemplo. Para terminar de crear la conexión, elija **Crear**.

    ![detalles de conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    La conexión se ha creado.

    ![creación de la conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message por nombre del acuerdo

Si decide codificar mensajes EDIFACT por nombre de contrato, abra la lista **Nombre del contrato EDIFACT** y escriba o seleccione el nombre del contrato EDIFACT existente. Escriba el mensaje XML para codificar.

![Escriba el nombre del contrato EDIFACT y el mensaje XML para codificar.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message por identidades

Si elige codificar mensajes EDIFACT por identidad, especifique el identificador y el calificador del remitente, así como el identificador y el calificador del receptor, tal y como están configurados en el contrato EDIFACT. Seleccione el mensaje XML para codificar.

![Proporcione la identidad del remitente y del receptor, y seleccione el mensaje XML para codificar.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalles de la codificación de EDIFACT

El conector de codificación EDIFACT lleva a cabo estas tareas: 

* Resolver el acuerdo haciendo coincidir el calificador y el identificador del remitente con el calificador y el identificador del receptor del receptor.
* Serializa el intercambio EDI, convirtiendo los mensajes codificados en XML en conjuntos de transacciones EDI en el intercambio.
* Aplica segmentos de encabezado y finalizador del conjunto de transacciones.
* Genera un número de control de intercambio, un número de control de grupo y un número de control del conjunto de transacciones para cada intercambio de salida.
* Reemplaza los separadores en los datos de carga útil.
* Valida propiedades EDI y específicas del asociado.
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje.
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones.
  * Validación extendida realizada en los elementos de datos del conjunto de transacciones.
* Genera un documento XML para cada conjunto de transacciones.
* Solicita una confirmación técnica o funcional (si esta opción está configurada).
  * Como confirmación técnica, el mensaje CONTRL indica la recepción de un intercambio.
  * Como confirmación funcional, el mensaje CONTRL indica la aceptación o el rechazo del intercambio, el grupo o el mensaje recibido, con una lista de errores o una funcionalidad no admitida.

## <a name="view-swagger-file"></a>Ver el archivo de Swagger
Para ver los detalles de Swagger para el conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 

