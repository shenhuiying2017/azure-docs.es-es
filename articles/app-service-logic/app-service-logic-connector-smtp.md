<properties
   pageTitle="Aplicación de API del conector de SMTP"
   description="Uso del conector de SMTP"
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
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# Conector de SMTP

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo de trabajo.

El conector de SMTP permite conectarse a un servidor SMTP y realizar una acción para enviar correo electrónico con datos adjuntos. La acción "Enviar correo electrónico" del conector de SMTP permite enviar correo electrónico a las direcciones de correo electrónico especificadas.

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

## Uso del conector de SMTP en la aplicación lógica
Una vez creado el conector, ahora puede usar el conector de SMTP como acción para la aplicación lógica. Para ello, siga estos pasos:

1.	Cree una nueva aplicación lógica.

	![][2]
2.	Abra **Desencadenadores y acciones** para abrir el diseñador de aplicaciones lógicas y configurar el flujo de trabajo.

	![][3]
3.	El conector de SMTP aparecerá en la sección "Aplicaciones de API de este grupo de recursos" en la galería, en el lado derecho. Selecciónelo:

	![][4]
4.	Seleccione el conector de SMTP para agregarlo automáticamente al diseñador de flujo de trabajo.

Ahora puede configurar el conector de SMTP para usarlo en el flujo de trabajo. Seleccione la acción **Enviar correo electrónico** y configure las propiedades de entrada como sigue:

	Propiedad | Descripción
	--- | ---
	To | Especifique la dirección de correo electrónico de los destinatarios. Separe varias direcciones de correo electrónico mediante un punto y coma (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Especifique la dirección de correo electrónico de los destinatarios de copia oculta. Separe varias direcciones de correo electrónico mediante un punto y coma (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Especifique el asunto del correo electrónico.
	Body | Escriba el cuerpo del correo electrónico.
	Is HTML | Si esta propiedad está establecida en true, el contenido del cuerpo se envía como HTML.
	Bcc | Especifique la dirección de correo electrónico de los destinatarios para la copia de carbón oculta. Separe varias direcciones de correo electrónico mediante un punto y coma (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Especifique la importancia del correo electrónico. Las opciones son Normal, Baja y Alta.
	Attachments | Datos adjuntos que se deben enviar con el correo electrónico. Contiene los campos siguientes: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Cree las aplicaciones de API mediante las API de REST. Consulte [Referencia sobre conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO5-->