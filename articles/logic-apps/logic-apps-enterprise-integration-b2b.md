---
title: "Creación de soluciones B2B con Enterprise Integration Pack | Microsoft Docs"
description: "Obtenga información sobre cómo recibir datos utilizando las características B2B de paquete de Enterprise Integration Pack."
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
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: cb8cac0c63930139760617d721faffeef70cfbec


---
# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>Obtenga información sobre cómo recibir datos utilizando las características B2B de paquete de Enterprise Integration Pack.
## <a name="overview"></a>Información general
Este documento forma parte de Enterprise Integration Pack de Aplicaciones lógicas. Consulte la información general para más información sobre las [funcionalidades de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos
Para utilizar las acciones AS2 y X12, necesitará una cuenta de integración de empresas.

[Procedimiento para crear una cuenta de Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Procedimiento para utilizar los conectores B2B de Aplicaciones lógicas
Cuando haya creado una cuenta de integración y agregado partners y contratos a ella, ya podrá crear una Aplicación lógica que implemente un flujo de trabajo de negocio a negocio (B2B).

En este tutorial verá cómo usar las acciones AS2 y X12 para crear una Aplicación lógica de negocio a negocio que reciba datos de un socio comercial.

1. Cree una nueva aplicación lógica y [vincúlela a su cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md).  
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica  
   ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)  
3. Agregue la acción **Decode AS2** (Descodificar AS2), pero seleccione antes **Agregar una acción**  
   ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)  
4. Escriba la palabra **as2** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)  
5. Seleccione la acción **AS2 - Decode AS2 message**  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)  
6. Tal y como se muestra, agregue el **cuerpo** que tomará como entrada. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que desencadenó la Aplicación lógica. También puede escribir una expresión para especificar los encabezados en el campo**ENCABEZADOS** :
   
    @triggerOutputs()['headers']
7. Agregue los **encabezados** que se necesitan para AS2. Estos serán los encabezados de solicitud HTTP. En este ejemplo, seleccione los encabezados de la solicitud HTTP que desencadenó la Aplicación lógica.
8. Ahora, agregue la acción Decode X12 message, para lo que debe volver a seleccionar **Agregar una acción**  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)   
9. Escriba la palabra **x12** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)  
10. Seleccione la acción **X12 - Decode X12 message** que va a agregar a la aplicación lógica  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)  
11. Ahora debe especificar la entrada a esta acción que, a su vez, será la salida de la acción AS2 anterior. El contenido real del mensaje se encuentra en un objeto JSON con codificación Base64. Por lo tanto, debe especificar una expresión como entrada para escribir la siguiente expresión en el campo de entrada **X12 FLAT FILE MESSAGE TO DECODE** (MENSAJE DE ARCHIVO SIN FORMATO X12 PARA DESCODIFICAR).  
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  
12. Con este paso se descodificarán los datos X12 recibidos del socio comercial y se obtendrá como resultado varios elementos en un objeto JSON. Para informar a los partners de la recepción de los datos, puede volver a enviar una respuesta que contenga la notificación de disposición del mensaje (MDN) AS2 en una acción de respuesta HTTP.  
13. Agregar la acción **Response** (Respuesta), para lo que debe seleccionar **Agregar una acción**   
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)  
14. Escriba la palabra **response** en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)  
15. Seleccione la acción **Response** (Respuesta) para agregarla  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)  
16. Establezca el campo **CUERPO** de la respuesta mediante la expresión siguiente para acceder a la MDN de la salida de la acción **Decode X12 message**  
    
    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

1. Guarde el trabajo  
   ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

En este momento, ya ha terminado de configurar su Aplicación lógica B2B. En una aplicación real, puede almacenar los datos X12 descodificados en un almacén de datos o una aplicación LOB. Para ello, puede agregar fácilmente más acciones o escribir API personalizadas que se conecten a sus propias aplicaciones LOB, así como emplear estas API en su Aplicación lógica.

## <a name="features-and-use-cases"></a>Características y casos de uso
* Gracias a las acciones de codificación y descodificación AS2 y X12, puede recibir y enviar datos con los socios comerciales como remitentes y destinatarios a través de protocolos estándar del sector utilizando Aplicaciones lógicas.  
* Puede utilizar AS2 y X12 de forma independiente o juntos para intercambiar datos con socios comerciales según se requiera.
* Las acciones B2B facilitan el proceso de creación de socios comerciales y contratos en la cuenta de integración, así como su uso en una Aplicación lógica.  
* Al ampliar su Aplicación lógica con otras acciones, podrá enviar y recibir datos con otras aplicaciones y servicios (por ejemplo, Salesforce) como origen y destino.  

## <a name="learn-more"></a>Más información
[Más información acerca de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)  




<!--HONumber=Jan17_HO3-->


