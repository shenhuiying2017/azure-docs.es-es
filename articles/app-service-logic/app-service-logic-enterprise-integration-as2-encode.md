<properties 
	pageTitle="Obtenga información sobre el conector Encode AS2 Message de Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Introducción a Encode AS2 Message

Conéctese a Encode AS2 Message para garantizar la seguridad y confiabilidad al transmitir mensajes. Ofrece firma digital, cifrado y confirmaciones mediante las notificaciones de disposición del mensaje (MDN), lo que también conlleva una compatibilidad con el no rechazo.

## Creación de la conexión

### Requisitos previos

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)

* Se requiere una cuenta de integración para usar el conector Encode AS2 Message. Obtenga más información sobre cómo crear una [cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [asociados](./app-service-logic-enterprise-integration-partners.md) y un [contrato de AS2](./app-service-logic-enterprise-integration-as2.md).

### Conéctese a Encode AS2 Message siguiendo estos pasos:

1. En [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](./app-service-logic-create-a-logic-app.md), podemos ver un ejemplo.

2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud. En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción. Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "AS2" en el cuadro de búsqueda. Seleccione AS2 – Encode AS2 Message (AS2: Encode AS2 Message).

	![buscar AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión:
	
	![crear la conexión con la cuenta de integración](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)

4. Escriba los detalles de la cuenta de integración. Las propiedades con un asterisco son obligatorias.

	| Propiedad | Detalles |
	| --------   | ------- |
	| Nombre de la conexión * | Escriba cualquier nombre para la conexión. |
	| Cuenta de integración* | Escriba el nombre de cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |

  	Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:

  	![conexión de integración establecida](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)

5. Seleccione **Crear**.

6. Observe que la conexión se ha creado en el portal. Especifique los identificadores AS2-From y AS2-To (como se hayan configurado en el contrato), y los detalles del cuerpo (la carga útil del mensaje).

	![especificar los campos obligatorios](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## La codificación AS2 realiza lo siguiente:

* Aplica los encabezados AS2/HTTP.
* Firma los mensajes salientes (en caso de haberse configurado).
* Cifra los mensajes salientes (en caso de haberse configurado).
* Comprime el mensaje (en caso de haberse configurado).

##Inténtelo usted mismo

¿Por qué no probarlo? Haga clic [aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) para implementar una aplicación lógica totalmente operativa de su propiedad con las características AS2 de Logic Apps.
 
## Pasos siguientes

[Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0824_2016-->