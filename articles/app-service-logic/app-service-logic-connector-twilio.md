<properties
   pageTitle="Aplicación de API del conector de Twilio"
   description="Uso del conector de Twilio"
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
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Uso del conector de Twilio en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de Twilio permite enviar y recibir SMS en su cuenta de Twilio. También permite recuperar números de teléfono y datos de uso.

## Creación de un conector de Twilio para la aplicación lógica ##
Para usar el conector de Twilio, deberá crear primero una instancia de la aplicación de API del conector de Twilio. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a “Aplicaciones de API” y busque “Conector Twilio”.
3.	Configure el conector de Twilio de la siguiente forma:
 
	![][1]
	- **Ubicación**: elija la ubicación geográfica en que desearía implementar el conector.
	- **Suscripción**: elija una suscripción en que desea crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en que debe residir el conector.
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Twilio.
	- **Configuración del paquete**
		- **SID de la cuenta**: el identificador exclusivo de la cuenta. El SID de la cuenta puede obtenerse desde <https://www.twilio.com/user/account/settings>
		- **Token de autorización**: token de autorización asociado con la cuenta. El token de autorización para la cuenta se puede recuperar desde <https://www.twilio.com/user/account/settings>


4.	Haga clic en Crear. Se creará un nuevo conector de Twilio.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Twilio.

## Uso del conector de Twilio en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Twilio como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Twilio.

	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

	![][3]
3.	El conector de Twilio aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

	![][4]
4. Puede quitar la aplicación de la API del conector de Twilio en el editor haciendo clic en "Conector de Twilio".

5.	Ahora puede usar el conector de Twilio en el flujo. Puede usar la acción "Enviar mensaje" en el flujo para enviar un mensaje. Configure las propiedades de entrada para la acción "Enviar mensaje" de la siguiente manera:
	- **Desde número de teléfono**: escriba un número de teléfono de Twilio habilitado para el tipo de mensaje que desee enviar. Solo los números de teléfono o códigos breves adquiridos a través de Twilio funcionarán con este conector.
	- **A número de teléfono**: el número de teléfono de destino. Formato aceptado: +, seguido por el código de país y, a continuación, el número de teléfono. Por ejemplo, +16175551212. Si omite el +, Twilio utilizará el código de país que escribió en “Desde” número.
	- **Texto**: el texto del mensaje que desea enviar.

	![][5] 
	![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=August15_HO8-->