<properties 
	pageTitle="Obtenga información sobre el conector de Decode AS2 Message de Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
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

# Introducción a Decode AS2 Message

Conéctese a Decode AS2 Message para establecer seguridad y confiabilidad al transmitir mensajes. Proporciona firma digital, descifrado y confirmaciones a través de las notificaciones de disposición de mensajes (MDN).

## Creación de la conexión

### Requisitos previos

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)

* Se requiere una cuenta de integración para usar el conector de Decode AS2 Message. Vea los detalles sobre cómo crear una [cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [asociados](./app-service-logic-enterprise-integration-partners.md) y [un acuerdo AS2](./app-service-logic-enterprise-integration-as2.md).

### Conecte con Decode AS2 Message mediante los siguientes pasos:

1. En [Creación de una aplicación lógica](./app-service-logic-create-a-logic-app.md), podemos ver un ejemplo.

2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud. En el diseñador de aplicaciones lógicas, agregue un desencadenador y, luego, agregue una acción. Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y, luego, escriba "AS2" en el cuadro de búsqueda. Seleccione AS2 – Decode AS2 Message

	![Buscar AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión.

	![Crear conexión de integración](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Escriba los detalles de la cuenta de integración. Las propiedades con un asterisco son obligatorias.

	| Propiedad | Detalles |
	| --------   | ------- |
	| Nombre de la conexión * | Escriba un nombre para la conexión. |
	| Integration Account (Cuenta de integración)* | Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |

  	Una vez completados, los detalles de la conexión presentan un aspecto similar al siguiente.

  	![conexión de integración](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Seleccione **Crear**.
	
6. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica

	![conexión de integración creada](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png)

7. Seleccione el cuerpo y los encabezados de las salidas de la solicitud

	![especificar los campos obligatorios](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png)

## AS2 Decode hace lo siguiente:

* Procesa encabezados AS2/HTTP.
* Comprueba la firma (si la opción está configurada).
* Descifra los mensajes (si la opción está configurada).
* Descomprime el mensaje (si la opción está configurada).
* Reconcilia un MDN recibido con el mensaje de salida original.
* Actualiza y correlaciona registros en la base de datos sin repudio.
* Escribe registros para los informes de estado de AS2.
* El contenido de la carga útil de salida está codificado en base64.
* Determina si se requiere un MDN y si este debe ser sincrónico o asincrónico en función de la configuración del acuerdo AS2.
* Genera un MDN sincrónico o asincrónico (en función de las configuraciones del acuerdo).
* Establece las propiedades y los token de correlación en el MDN.

##Inténtelo usted mismo

¿Por qué no probarlo? Haga clic [aquí](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implementar una aplicación lógica totalmente operativa de su propiedad con las características AS2 de las Aplicaciones lógicas.

## Pasos siguientes

[Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0914_2016-->