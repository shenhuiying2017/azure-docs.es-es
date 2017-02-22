---
title: "Codificación de mensajes X12 en Azure Logic Apps | Microsoft Docs"
description: Uso del codificador X12 de Enterprise Integration Pack en Logic Apps
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>Introducción a Encode X12 Message
Valida propiedades EDI y específicas del asociado, convierte los mensajes con codificación XML en conjuntos de transacciones EDI en el intercambio y solicita una confirmación técnica o funcional.

## <a name="prereqs"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Se requiere una cuenta de integración para usar el conector de Encode X12 Message. Consulte información detallada acerca de cómo crear una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), un [asociado](logic-apps-enterprise-integration-partners.md) y un [contrato de X12](logic-apps-enterprise-integration-x12.md).

## <a name="encode-x12-messages"></a>Codificación de mensajes X12

1. [Cree una aplicación lógica](logic-apps-create-a-logic-app.md).
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "x12" en el cuadro de búsqueda.  Seleccione X12 - Encode X12 Message por el nombre del acuerdo o X12 - Encode to x12 Message por identidades:  
   
    ![buscar x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión:
   
    ![conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias:
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
    Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:
   
    ![creación de la conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. Seleccione **Crear**.
6. Observe que la conexión se ha creado en el portal.
   
    ![detalles de conexión de la cuenta de integración](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 Message por nombre de acuerdo
Seleccione el acuerdo X12 del desplegable y el mensaje XML para codificar:     
    ![especificar los campos obligatorios](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>Encode X12 Message por identidades
Especifique el identificador y el calificador del remitente y el identificador y el calificador del receptor tal y como están configurados en el acuerdo X12.  Seleccione el mensaje XML para codificar:
   
   ![especificar los campos obligatorios](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Detalles de la codificación de X12

El conector X12 Encode hace lo siguiente:

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

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


