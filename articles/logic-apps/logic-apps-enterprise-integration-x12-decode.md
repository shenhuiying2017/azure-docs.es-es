---
title: "Información acerca del conector Decode X12 Message de Enterprise Integration Pack | Microsoft Docs"
description: "Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack"
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
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: b6ce911b8435f3d6ed1c12c78ba81abe180346a2


---
# <a name="get-started-with-decode-x12-message"></a>Introducción a Decode X12 Message
Valida propiedades EDI y específicas de asociados, genera un documento XML para cada conjunto de transacciones y origina la confirmación de transacción procesada.

## <a name="create-the-connection"></a>Creación de la conexión
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Se requiere una cuenta de integración para usar el conector de Decode X12 Message. Consulte cómo crear una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [asociados](../logic-apps/logic-apps-enterprise-integration-partners.md) y un [contrato X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Conecte con Decode X12 Message mediante los siguientes pasos:
1. En [Creación de una nueva aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) se puede ver un ejemplo
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "x12" en el cuadro de búsqueda.  Seleccione X12 – Decode X12 Message.
   
    ![buscar x12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión.
   
    ![conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)     
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias.
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
    Una vez completados, los detalles de la conexión presentan un aspecto similar al siguiente:
   
    ![creación de la conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 
5. Seleccione **Crear**
6. Observe que la conexión se ha creado en el portal.
   
    ![detalles de conexión de la cuenta de integración](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 
7. Seleccione X12 flat file message to decode (Mensaje de archivo plano X12 para descodificar).
   
    ![especificar los campos obligatorios](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>Funciones de X12 Decode
* Valida el sobre con el acuerdo de socio comercial.
* Genera un documento XML para cada conjunto de transacciones.
* Valida propiedades EDI y específicas del asociado.
  * Validación estructural de EDI y validación de esquema extendido
  * Validación de la estructura del sobre de intercambio
  * Validación del esquema del sobre con respecto al esquema de control
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones 
* Comprueba que los números de control de intercambio, grupo y conjunto de transacciones no están duplicados.
  * Comprueba el número de control del intercambio en relación con los intercambios recibidos anteriormente.
  * Comprueba el número de control del grupo en relación con otros números de control de grupo en el intercambio.
  * Comprueba el número de control del conjunto de transacciones en relación con otros números de control del conjunto de transacciones de ese grupo.
* Convierte todo el intercambio a XML. 
  * Dividir intercambio como conjuntos de transacciones - suspender conjuntos de transacciones en caso de error: analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, X12 Decode suspende solo esos conjuntos de transacciones.
  * Dividir intercambio como conjuntos de transacciones - suspender intercambio en caso de error: analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente.  Si uno o varios conjuntos de transacciones del intercambio no superan la validación, X12 Decode suspende todo el intercambio.
  * Conservar intercambio - suspender conjuntos de transacciones en caso de error: crea un documento XML para todo el intercambio por lotes. X12 Decode suspende solo los conjuntos de transacciones que no superan la validación, sin dejar de procesar el resto de conjuntos de transacciones.
  * Conservar intercambio - suspender intercambio en caso de error: crea un documento XML para todo el intercambio por lotes. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, X12 Decode suspende todo el intercambio. 
* Genera una confirmación técnica o funcional (si esta opción está configurada).
  * Se genera una confirmación técnica como resultado de la validación del encabezado. La confirmación técnica informa del estado del procesamiento de un encabezado y finalizador de intercambio por parte del receptor de la dirección.
  * Se genera una confirmación funcional como resultado de la validación del cuerpo. La confirmación funcional informa de cada error encontrado al procesar el documento recibido.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 




<!--HONumber=Jan17_HO3-->


