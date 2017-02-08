---
title: "Información acerca del conector Decode EDIFACT Message de Enterprise Integration Pack | Microsoft Docs"
description: "Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack"
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
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8bf5fe5527e3de6f2e0950b3ee494b88cd1063a1
ms.openlocfilehash: 96dbedf2dc072795712e4b0a41c424e633263220


---
# <a name="get-started-with-decode-edifact-message"></a>Introducción a Decode EDIFACT Message
Valida las propiedades de EDI y las específicas de los partners, genera un documento XML para cada conjunto de transacciones, así como la confirmación de transacción procesada.

## <a name="create-the-connection"></a>Creación de la conexión
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Para usar el conector Decode EDIFACT Message, se requiere una cuenta de integración. Consulte cómo crear una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), [asociados](logic-apps-enterprise-integration-partners.md) y un [contrato de EDIFACT](logic-apps-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Conecte con Encode EDIFACT Message mediante los siguientes pasos:
1. En [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](logic-apps-create-a-logic-app.md) se puede ver un ejemplo.
2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud.  En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción.  Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y escriba "EDIFACT" en el cuadro de búsqueda.  Seleccione Decode EDIFACT Message
   
    ![buscar EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión.
   
    ![crear cuenta de integración](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)  
4. Escriba los detalles de la cuenta de integración.  Las propiedades con un asterisco son obligatorias.
   
   | Propiedad | Detalles |
   | --- | --- |
   | Nombre de la conexión * |Escriba cualquier nombre para la conexión. |
   | Cuenta de integración* |Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |
   
    Una vez completados, los detalles de la conexión presentan un aspecto similar al siguiente.
   
    ![cuenta de integración creada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  
5. Seleccione **Crear**
6. Observe que la conexión se ha creado en el portal.
   
    ![detalles de conexión de cuenta de integración](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  
7. Seleccione EDIFACT flat file message to decode (Mensaje de archivo plano de EDIFACT que se va a descodificar).
   
    ![especificar campos obligatorios](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>Funciones de EDIFACT Decode
* Resuelve el acuerdo, para lo que hace coincidir el calificador e identificador del remitente con el calificador e identificador del receptor.
* Divide varios intercambios de un único mensaje en intercambios independientes.
* Valida el sobre con el acuerdo de socio comercial.
* Desensambla el intercambio.
* Valida las propiedades de EDI y específicas del partner, lo que incluye
  * Validación de la estructura del sobre de intercambio.
  * Validación del esquema del sobre con respecto al esquema de control
  * Validación del esquema de los elementos de datos del conjunto de transacciones con respecto al esquema de mensaje
  * Validación de EDI realizada en los elementos de datos del conjunto de transacciones
* Comprueba que los números de control de intercambio, grupo y conjunto de transacciones no están duplicados (si está configurado). 
  * Comprueba el número de control del intercambio con los intercambios recibidos anteriormente. 
  * Comprueba el número de control del grupo en relación con otros números de control de grupo en el intercambio. 
  * Comprueba el número de control del conjunto de transacciones con otros números de control del conjunto de transacciones de dicho grupo.
* Genera un documento XML para cada conjunto de transacciones.
* Convierte todo el intercambio a XML. 
  * Dividir intercambio como conjuntos de transacciones (suspender conjuntos de transacciones en caso de error): analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende solo esos conjuntos de transacciones. 
  * Dividir intercambio como conjuntos de transacciones (suspender intercambio en caso de error): analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente.  Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende todo el intercambio.
  * Conservar intercambio (suspender conjuntos de transacciones en caso de error): crea un documento XML para todo el intercambio por lotes. EDIFACT Decode suspende solo los conjuntos de transacciones que no superan la validación, pero no deja de procesar los restantes conjuntos de transacciones.
  * Conservar intercambio (suspender intercambio en caso de error): crea un documento XML para todo el intercambio por lotes. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende todo el intercambio. 
* Genera una confirmación técnica (control) o funcional (si esta opción está configurada).
  * Una confirmación técnica o ACK CONTRL informa de los resultados de una comprobación sintáctica de todo el intercambio recibido.
  * Una confirmación funcional confirma la aceptación o el rechazo de un intercambio recibido o un grupo

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") 




<!--HONumber=Jan17_HO3-->


