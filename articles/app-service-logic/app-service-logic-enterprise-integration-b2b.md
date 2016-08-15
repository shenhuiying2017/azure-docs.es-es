<properties 
	pageTitle="Creación de soluciones B2B con Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Obtenga información sobre cómo recibir datos utilizando las características B2B de paquete de Enterprise Integration Pack." 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Obtenga información sobre cómo recibir datos utilizando las características B2B de paquete de Enterprise Integration Pack.#

## Información general ##

Este documento forma parte de Enterprise Integration Pack de Aplicaciones lógicas. Consulte la información general para obtener más información sobre las [funcionalidades de Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Requisitos previos ##

Para utilizar las acciones AS2 y X12, necesitará una cuenta de integración de empresas.

[Procedimiento para crear una cuenta de integración de empresas](./app-service-logic-enterprise-integration-accounts.md)

## Procedimiento para utilizar los conectores B2B de Aplicaciones lógicas ##

Cuando haya creado una cuenta de integración y agregado partners y contratos a ella, ya podrá crear una Aplicación lógica que implemente un flujo de trabajo de negocio a negocio (B2B).

En este tutorial verá cómo usar las acciones AS2 y X12 para crear una Aplicación lógica de negocio a negocio que reciba datos de un socio comercial.

1. Cree una nueva Aplicación lógica y [vincúlela a su cuenta de integración](./app-service-logic-enterprise-integration-accounts.md).
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la Aplicación lógica. ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)
3. Agregue la acción **Decode AS2** (Descodificar AS2) seleccionando primero **Agregar una acción**. ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)
4. Escriba la palabra **as2** en el cuadro de búsqueda para filtrar todas las acciones que quiera usar. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)
6. Seleccione la acción **AS2 - Decode AS2 message** (AS2: descodificar mensaje AS2). ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)
7. Tal y como se muestra, agregue el **cuerpo** que tomará como entrada. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que desencadenó la Aplicación lógica. También puede escribir una expresión para especificar los encabezados en el campo **ENCABEZADOS**:

    @triggerOutputs()['headers']

8. Agregue los **encabezados** que se necesitan para AS2. Estos serán los encabezados de solicitud HTTP. En este ejemplo, seleccione los encabezados de la solicitud HTTP que desencadenó la Aplicación lógica.
9. Ahora, agregue la acción Decode X12 message (Descodificar mensaje X12) seleccionando de nuevo **Agregar una acción**. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)
10. Escriba la palabra **x12** en el cuadro de búsqueda para filtrar todas las acciones que quiera usar. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)
11. Seleccione la acción **X12 - Decode X12 message** (X12: descodificar mensaje X12) que va a agregar a la Aplicación lógica ![](./media/app-service-logic-enterprise-integration-b2b/b2b-11.png)
12. Ahora debe especificar la entrada a esta acción que, a su vez, será la salida de la acción AS2 anterior. El contenido real del mensaje se encuentra en un objeto JSON con codificación Base64. Por lo tanto, debe especificar una expresión como entrada para escribir la siguiente expresión en el campo de entrada **X12 FLAT FILE MESSAGE TO DECODE** (MENSAJE DE ARCHIVO SIN FORMATO X12 PARA DESCODIFICAR).

    @base64ToString(body('Decode\_AS2\_message')?['Message']?['Content'])

13. Con este paso se descodificarán los datos X12 recibidos del socio comercial y se obtendrá como resultado varios elementos en un objeto JSON. Para informar a los partners de la recepción de los datos, puede volver a enviar una respuesta que contenga la notificación de disposición del mensaje (MDN) AS2 en una acción de respuesta HTTP.
14. Agregar la acción **Respuesta** seleccionando **Agregar una acción**. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)
15. Escriba la palabra **response** en el cuadro de búsqueda para filtrar todas las acciones que quiera usar. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)
16. Seleccione la acción **Respuesta** para agregarla. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)
17. Establezca el campo **CUERPO** de la respuesta mediante la expresión siguiente para acceder a la MDN de la salida de la acción **Decode X12 message** (Descodificar mensaje X12).

    @base64ToString(body('Decode\_AS2\_message')?['OutgoingMdn']?['Content'])

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)
18. Guarde el trabajo. ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)

En este momento, ya ha terminado de configurar su Aplicación lógica B2B. En una aplicación real, puede almacenar los datos X12 descodificados en un almacén de datos o una aplicación LOB. Para ello, puede agregar fácilmente más acciones o escribir API personalizadas que se conecten a sus propias aplicaciones LOB, así como emplear estas API en su Aplicación lógica.

## Características y casos de uso ##

- Gracias a las acciones de codificación y descodificación AS2 y X12, puede recibir y enviar datos con los socios comerciales como remitentes y destinatarios a través de protocolos estándar del sector utilizando Aplicaciones lógicas.
- Puede utilizar AS2 y X12 de forma independiente o juntos para intercambiar datos con socios comerciales según se requiera.
- Las acciones B2B facilitan el proceso de creación de socios comerciales y contratos en la cuenta de integración, así como su uso en una Aplicación lógica.
- Al ampliar su Aplicación lógica con otras acciones, podrá enviar y recibir datos con otras aplicaciones y servicios (por ejemplo, Salesforce) como origen y destino.

## Más información ##

[Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0803_2016-->