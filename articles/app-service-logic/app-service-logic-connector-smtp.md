<properties
   pageTitle="Uso del conector de SMTP en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de SMTP o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="andalmia"/>


# Introducción al conector de SMTP y su incorporación a su aplicación lógica
Conéctese a un servidor SMTP para enviar correos electrónicos, incluidos mensajes con datos adjuntos. La acción "Enviar correo electrónico" del conector de SMTP permite enviar correo electrónico a las direcciones de correo electrónico especificadas.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo de trabajo. Puede agregar el conector de SMTP a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.


## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualice un pedido o se agregue un nuevo cliente, enviar un correo electrónico al nuevo cliente.

El conector de SMTP puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. Actualmente, no hay ningún desencadenador para este conector.

El conector de SMTP dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | Enviar correo electrónico


## Creación del conector de SMTP
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de SMTP".
3. Haga clic en **Crear** para crear el conector.
4. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
5. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
	--- | --- | ---
	User Name | Sí | Escriba un nombre de usuario que se puede conectar al servidor SMTP.
	Password | Sí | Escriba la contraseña del nombre de usuario.
	Dirección del servidor | Sí | Escriba el nombre o la dirección IP del servidor SMTP.
	Puerto del servidor | Sí | Escriba el número de puerto del servidor SMTP.
	Habilitación de SSL | No | Escriba *true* para usar SMTP a través de un canal seguro de SSL/TLS.

6. Seleccione **Crear**.

> [AZURE.IMPORTANT]Algunos servidores SMTP pueden tener problemas con el funcionamiento de este conector (SendGrid y Gmail). Si desea enviar correo desde SendGrid, nuestro [repositorio de GitHub](https://github.com/logicappsio/SendGridAPI) tiene una API personalizada que interactuará directamente con las API de SendGrid.

## Uso del conector de SMTP en la aplicación lógica
Una vez creado el conector, ahora puede usar el conector de SMTP como acción para la aplicación lógica. Para ello, siga estos pasos:

1.	Creación de una nueva aplicación lógica: ![][2]
2.	Abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configurar el flujo de trabajo: ![][3]
3.	El conector de SMTP aparecerá en la sección "Aplicaciones de API de este grupo de recursos" en la galería, en el lado derecho. Selecciónelo: ![][4]
4.	Seleccione el conector de SMTP para agregarlo automáticamente al diseñador de flujo de trabajo.

Ahora puede configurar el conector de SMTP para usarlo en el flujo de trabajo. Seleccione la acción **Enviar correo electrónico** y configure las propiedades de entrada:

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

![][5] ![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=Oct15_HO3-->