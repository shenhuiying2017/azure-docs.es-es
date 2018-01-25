---
title: "Descodificación de mensajes EDIFACT: Azure Logic Apps | Microsoft Docs"
description: Valide EDI y genere confirmaciones con el descodificador de mensajes EDIFACT en Enterprise Integration Pack para Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 41bd0291f3ff8f5dd61a21d1ba4ecb3b60a3cbfb
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificación de mensajes EDIFACT para Azure Logic Apps con Enterprise Integration Pack

Con el conector de descodificación de mensajes EDIFACT, puede validar propiedades EDI y específicas de asociados, dividir intercambios en transacciones o conservar intercambios completos y originar la confirmación de las transacciones procesadas. Para usar este conector, debe agregarlo a un desencadenador existente en la aplicación lógica.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de descodificación de mensajes EDIFACT. 
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato EDIFACT](logic-apps-enterprise-integration-edifact.md) ya definido en la cuenta de integración.

## <a name="decode-edifact-messages"></a>Descodificación de mensajes EDIFACT

1. [Crear una aplicación lógica](quickstart-create-first-logic-app-workflow.md).

2. El conector de descodificación de mensajes EDIFACT no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, por ejemplo, un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3. En el cuadro de búsqueda, escriba "EDIFACT" para el filtro. Seleccione **Descodificar mensaje EDIFACT**.
   
    ![buscar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar.
   
    ![crear cuenta de integración](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

4. Cuando haya acabado, para terminar de crear la conexión, elija **Crear**. Los detalles de la conexión deberían ser similares a los de este ejemplo:

    ![detalles de la cuenta de integración](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Una vez creada la conexión, como se muestra en este ejemplo, seleccione el mensaje de archivo plano EDIFACT que desee descodificar.

    ![creación de la conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por ejemplo: 

    ![Selección del mensaje de archivo plano EDIFACT para descodificar](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalles de descodificador de EDIFACT

El conector de descodificación EDIFACT lleva a cabo estas tareas: 

* Valida el sobre con el acuerdo entre socios comerciales.
* Resuelve el acuerdo, para lo que hace coincidir el calificador e identificador del remitente con el calificador e identificador del receptor.
* Divide un intercambio en varias transacciones cuando el intercambio tiene más de una transacción que se basa en la configuración de recepción del acuerdo.
* Desensambla el intercambio.
* Valida las propiedades de EDI y específicas del asociado, lo que incluye:
  * Validación de la estructura del sobre de intercambio
  * Validación del esquema del sobre con respecto al esquema de control
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones
* Comprueba que los números de control de intercambio, grupo y conjunto de transacciones no están duplicados (si está configurado). 
  * Comprueba el número de control del intercambio con los intercambios recibidos anteriormente. 
  * Comprueba el número de control del grupo en relación con otros números de control de grupo en el intercambio. 
  * Comprueba el número de control del conjunto de transacciones con otros números de control del conjunto de transacciones de dicho grupo.
* Divide el intercambio en conjuntos de transacciones o conserva todo el intercambio:
  * Divide el intercambio como conjuntos de transacciones (suspende conjuntos de transacciones en caso de error): divide el intercambio en conjuntos de transacciones y analiza cada conjunto de transacciones. 
  La acción de descodificación X12 solo genera esos conjuntos de transacciones que no superan la validación para `badMessages` y los resultados de las transacciones restantes se establecen en `goodMessages`.
  * Divide el intercambio como conjuntos de transacciones (suspende el intercambio en caso de error): divide el intercambio en conjuntos de transacciones y analiza cada conjunto de transacciones. 
  Si uno o varios conjuntos de transacciones del intercambio no superan la validación, la acción de descodificación de X12 establece todos los conjuntos de transacciones del intercambio en `badMessages`.
  * Conserva el intercambio (suspende conjuntos de transacciones en caso de error): conserva el intercambio y procesa todo el intercambio por lotes. 
  La acción de descodificación X12 solo genera esos conjuntos de transacciones que no superan la validación para `badMessages` y los resultados de las transacciones restantes se establecen en `goodMessages`.
  * Conserva el intercambio (suspende el intercambio en caso de error): conserva el intercambio y procesa todo el intercambio por lotes. 
  Si uno o varios conjuntos de transacciones del intercambio no superan la validación, la acción X12 Decode establece todos los conjuntos de transacciones del intercambio en `badMessages`.
* Genera una confirmación técnica (control) o funcional (si esta opción está configurada).
  * Una confirmación técnica o ACK CONTRL informa de los resultados de una comprobación sintáctica de todo el intercambio recibido.
  * Una confirmación funcional confirma la aceptación o el rechazo de un intercambio recibido o un grupo

## <a name="view-swagger-file"></a>Ver el archivo de Swagger
Para ver los detalles de Swagger para el conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 

