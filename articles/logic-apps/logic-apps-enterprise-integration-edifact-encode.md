---
title: "Codificación de mensajes EDIFACT en Azure Logic Apps | Microsoft Docs"
description: Uso del codificador EDIFACT de Enterprise Integration Pack con Logic Apps
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>Introducción a Encode EDIFACT Message
Valida propiedades EDI y específicas del asociado. 

## <a name="prereqs"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Se requiere una cuenta de integración para usar el conector de Encode EDIFACT Message. Consulte cómo crear una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [asociados](logic-apps-enterprise-integration-partners.md) y un [contrato de EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>Codificación de mensajes EDIFACT
1. [Cree una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y escriba "EDIFACT" en el cuadro de búsqueda.  Seleccione Encode EDIFACT Message por el nombre del acuerdo o Encode to EDIFACT Message por identidades:
   
    ![buscar EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión:
   
    ![crear la conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias:
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
    Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:
   
    ![conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. Seleccione **Crear**.
6. Observe que la conexión se ha creado en el portal:
   
    ![detalles de conexión de la cuenta de integración](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message por nombre del acuerdo
Escriba el nombre del acuerdo de EDIFACT y el mensaje XML para codificar:
   
   ![especificar los campos obligatorios](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message por identidades
Escriba el identificador y el calificador del remitente así como el identificador y el calificador del receptor tal y como están configurados en el acuerdo EDIFACT. Seleccione el mensaje XML para codificar:  
    ![especificar los campos obligatorios](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Detalles de la codificación de EDIFACT
El conector Encode EDIFACT hace lo siguiente: 

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

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


