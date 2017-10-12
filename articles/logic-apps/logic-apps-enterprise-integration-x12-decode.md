---
title: "Descodificación de mensajes X12: Azure Logic Apps | Microsoft Docs"
description: Valide EDI y genere confirmaciones con el descodificador de mensajes X12 en Enterprise Integration Pack para Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 18719a8f49c74973947517161f7306c233a9323f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificación de mensajes X12 para Azure Logic Apps con Enterprise Integration Pack

Con el conector de descodificación de mensajes X12, puede validar el sobre con un acuerdo entre socios comerciales, validar propiedades EDI y específicas de asociados, dividir intercambios en conjuntos de transacciones o conservar intercambios completos y originar la confirmación de las transacciones procesadas. Para usar este conector, debe agregarlo a un desencadenador existente en la aplicación lógica.

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) que ya esté definida y asociada a su suscripción de Azure. Debe tener una cuenta de integración para usar el conector de descodificación de mensajes X12.
* Al menos dos [asociados](logic-apps-enterprise-integration-partners.md) que ya estén definidos en su cuenta de integración.
* Un [contrato X12](logic-apps-enterprise-integration-x12.md) ya definido en su cuenta de integración

## <a name="decode-x12-messages"></a>Descodificación de mensajes X12

1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).

2. El conector de descodificación de mensajes X12 no tiene desencadenadores, por lo que debe agregar uno para iniciar la aplicación lógica, como un desencadenador de solicitud. En el Diseñador de aplicaciones lógicas, agregue un desencadenador y una acción a la aplicación lógica.

3.  En el cuadro de búsqueda, escriba "x12" para el filtro. Seleccione **X12 - Decode X12 message** (X12 – Descodificación de mensajes X12).
   
    ![Búsqueda de "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Si no había creado ninguna conexión a la cuenta de integración, se le pedirá que lo haga ahora. Asigne un nombre a la conexión y seleccione la cuenta de integración que desee conectar. 

    ![Proporcionar los detalles de conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Aquellas propiedades con un asterisco son obligatorias.

    | Propiedad | Detalles |
    | --- | --- |
    | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
    | Cuenta de integración* |Escriba un nombre para la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentren en la misma ubicación de Azure. |

5.  Cuando haya terminado, los detalles de la conexión deberían parecerse a este ejemplo. Para terminar de crear la conexión, elija **Crear**.
   
    ![detalles de conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Una vez creada la conexión, como se muestra en este ejemplo, seleccione el mensaje de archivo plano X12 que desea descodificar.

    ![creación de la conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por ejemplo:

    ![Seleccione el mensaje de archivo plano X12 para descodificar](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>Detalles de X12 Decode

El conector de descodificación X12 lleva a cabo estas tareas:

* Valida el sobre con el acuerdo de socio comercial.
* Valida propiedades EDI y específicas del asociado.
  * Validación estructural de EDI y validación de esquema extendido
  * Validación de la estructura del sobre de intercambio
  * Validación del esquema del sobre con respecto al esquema de control
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones 
* Comprueba que los números de control de intercambio, grupo y conjunto de transacciones no están duplicados.
  * Comprueba el número de control del intercambio en relación con los intercambios recibidos anteriormente.
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
  Si uno o varios conjuntos de transacciones del intercambio no superan la validación, la acción de descodificación de X12 establece todos los conjuntos de transacciones del intercambio en `badMessages`. 
* Genera una confirmación técnica o funcional (si esta opción está configurada).
  * Se genera una confirmación técnica como resultado de la validación del encabezado. La confirmación técnica informa del estado del procesamiento de un encabezado y finalizador de intercambio por parte del receptor de la dirección.
  * Se genera una confirmación funcional como resultado de la validación del cuerpo. La confirmación funcional informa de cada error encontrado al procesar el documento recibido.

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 

