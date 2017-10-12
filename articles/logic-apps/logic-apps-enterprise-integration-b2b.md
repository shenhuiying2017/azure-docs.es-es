---
title: "Creación de soluciones B2B: Azure Logic Apps | Microsoft Docs"
description: "Recepción de datos en aplicaciones lógicas mediante las características B2B de Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Recepción de datos en aplicaciones lógicas con las características B2B de Enterprise Integration Pack

Después de crear una cuenta de integración que tiene asociados y contratos, está listo para crear un flujo de trabajo de negocio a negocio (B2B) para la aplicación lógica mediante [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Para utilizar las acciones AS2 y X12, debe disponer de una cuenta de Enterprise Integration. Aprenda a [crear una cuenta de Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Creación de una aplicación lógica con conectores B2B

Siga estos pasos para crear una aplicación lógica de B2B que use las acciones AS2 y X12 para recibir datos de un socio comercial:

1. Cree una aplicación lógica y [vincúlela a su cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Para agregar la acción **Decode AS2** (Descodificar AS2), seleccione **Agregar una acción**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Escriba la palabra **as2** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Seleccione la acción **AS2 - Decode AS2 message** (AS2: decodificar mensaje de AS2).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Agregue el **cuerpo** que desea usar como entrada. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que desencadena la aplicación lógica. O escriba una expresión que introduzca los encabezados en el campo **HEADERS**:

    @triggerOutputs()['headers']

7. Agregue los **encabezados** de AS2 que puede encontrar en los encabezados de la solicitud HTTP. En este ejemplo, seleccione los encabezados de la solicitud HTTP que desencadena la aplicación lógica.

8. Ahora agregue la acción Decode X12 message (Descodificar mensaje de X12). Seleccione **Add an action**(Agregar una acción).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Escriba la palabra **x12** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Seleccione la acción **X12 - Decode X12 message** (X12: descodificar mensaje de X12)

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Ahora debe especificar la entrada para esta acción. Esta entrada es la salida de la acción anterior de AS2.

    El contenido real del mensaje está en un objeto JSON y está codificado en base64, por lo que debe especificar una expresión como entrada. 
    Escriba la siguiente expresión en el campo de entrada **X12 FLAT FILE MESSAGE TO DECODE**:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Ahora, agregue los pasos para descodificar los datos X12 recibidos del socio comercial y dé salida a los elementos en un objeto JSON. 
    Para notificar al asociado la recepción de los datos, puede volver a enviar una respuesta que contenga la notificación de disposición del mensaje (MDN) AS2 en una acción de respuesta HTTP.

12. Para agregar la acción **Response** (Respuesta), debe seleccionar **Agregar una acción**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Escriba la palabra **response** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Seleccione la acción **Response** (Respuesta).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Establezca el campo **BODY** de la respuesta mediante la expresión siguiente para acceder a la MDN de la salida de la acción **Decode X12 message**:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Guarde el trabajo.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Con esto, ya ha terminado de configurar la aplicación lógica de B2B. En una aplicación real, puede almacenar los datos X12 descodificados en un almacén de datos o una aplicación de línea de negocio (LOB). Puede agregar más acciones o escribir API personalizadas que se conecten a sus propias aplicaciones LOB, así como emplear estas API en su aplicación lógica.

## <a name="features-and-use-cases"></a>Características y casos de uso

* Las acciones de descodificación y codificación de AS2 y X12 le permiten intercambiar datos entre los socios comerciales mediante protocolos estándar de la industria en aplicaciones lógicas.
* Puede utilizar AS2 y X12 de forma independiente o conjunta para intercambiar datos con socios comerciales.
* Las acciones B2B le ayudan a crear fácilmente socios comerciales y contratos en la cuenta de integración, así como a usarlos en una aplicación lógica.
* Al ampliar la aplicación lógica con otras acciones, podrá enviar y recibir datos entre otras aplicaciones y servicios como Salesforce.

## <a name="learn-more"></a>Más información
[Más información acerca de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
