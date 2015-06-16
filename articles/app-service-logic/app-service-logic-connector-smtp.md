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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Uso del conector de SMTP en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. 

El conector de SMTP permite conectarse a un servidor SMTP y realizar una acción para enviar correo electrónico con datos adjuntos.

- La acción "Enviar correo electrónico" del conector de SMTP permite enviar correo electrónico a las direcciones de correo electrónico especificadas. 

## Creación de un conector de SMTP para la aplicación lógica ##
Para usar el conector de SMTP, deberá crear primero una instancia de la aplicación de API del conector de SMTP. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de SMTP".
3.	Configure el conector de SMTP de la siguiente forma:
 
	![][1]
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de SMTP.
	- **Configuración del paquete**
		- **Nombre de usuario**: especifique el nombre de usuario para conectarse al servidor SMTP
		- **Contraseña**: especifique la contraseña para conectarse al servidor SMTP
		- **Dirección del servidor**: especifique el nombre del servidor SMTP o la dirección IP
		- **Puerto del servidor**: especifique el número de puerto del servidor SMTP.
		- **Habilitar SSL**: especifique true para usar SMTP a través del canal SSL/TLS seguro
4.	Haga clic en Crear. Se creará un nuevo conector de SMTP.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SMTP. 

## Uso del conector de SMTP en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de SMTP como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de SMTP.
 
	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo. 
 
	![][3]
3.	El conector de SMTP aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho. Selecciónelo.
 
	![][4]
4.	Puede quitar la aplicación de la API del conector de SMTP en el editor haciendo clic en "Conector de SMTP". 
	
7.	Ahora puede usar el conector de SMTP en el flujo. Seleccione la acción "Enviar correo electrónico" y configure las propiedades de entrada como sigue:
	- **Para** - La dirección de correo electrónico de los destinatarios. Separe múltiples direcciones de correo electrónico con un signo de punto y coma (;). Por ejemplo: recipient1@domain.com;recipient2@domain.com.
	- **Cc** - La dirección de correo electrónico de los destinatarios con copia. Separe múltiples direcciones de correo electrónico con un signo de punto y coma (;). Por ejemplo: recipient1@domain.com;recipient2@domain.com.
	- **Asunto** - El asunto del correo electrónico.
	- **Cuerpo** - El cuerpo del correo electrónico.
	- **Es HTML** - Cuando esta propiedad se establece en true, el contenido del cuerpo se enviará como HTML.
	- **CCO** - La dirección de correo electrónico de los destinatarios de copia oculta. Separe múltiples direcciones de correo electrónico con un signo de punto y coma (;). Por ejemplo: recipient1@domain.com;recipient2@domain.com.
	- **Importancia** - La importancia del correo electrónico. Las opciones son Normal, Baja, Alta.
	- **Datos adjuntos** - Los datos adjuntos que se enviarán con el correo electrónico. Contiene los campos siguientes:
		- Contenido (cadena)
		- Codificación de transferencia de contenido (Enum) ("ninguna"|"base64")
		- Tipo de contenido (cadena)
		- Id. de contenido (cadena)
		- Nombre de archivo (cadena)
	
	![][5] 
	![][6]


<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG


<!--HONumber=52--> 