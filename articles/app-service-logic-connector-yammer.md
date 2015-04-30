<properties 
   pageTitle="Aplicación de API del conector de Yammer" 
   description="Uso del conector de Yammer" 
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


# Uso del conector de Yammer en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. 

El conector de Yammer le permite conectarse a Yammer y realizar la acción Publicar mensaje y aplicar un desencadenador para recuperar el mensaje nuevo.

## Creación de un conector de Yammer para la aplicación lógica ##
Para usar el conector de Yammer, deberá crear primero crear una instancia de la aplicación de API del conector de Yammer. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior izquierda del Portal de Azure.
2.	Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de Yammer".
3.	Configure el conector de Yammer de la siguiente forma:
 
	![][1]
	- **Ubicación** - elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción** - elija una suscripción en la que desee crear este conector.
	- **Grupos de recursos** - seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan de hospedaje web** - seleccione o cree un plan de hospedaje web.
	- **Nivel de precios** - elija un nivel de precios para el conector.
	- **Nombre** - asigne un nombre al conector de Yammer. 

4.	Haga clic en Crear. Se creará un nuevo conector de Yammer.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de Yammer. 

## Uso del conector de Yammer en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Yammer como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Yammer.
 
	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo. 
 
	![][3]
3.	El conector de Yammer aparecerá en la sección "Aplicaciones de la API en este grupo de recursos" de la galería, en el lado derecho.
 
	![][4]
4. Puede colocar la aplicación de la API del conector de Yammer en el editor haciendo clic en "Conector de Yammer". Haga clic en el botón Autorizar. Proporcione las credenciales de Yammer. Haga clic en "Permitir".
 
	![][5]
	![][6]
	![][7]
5.	Ahora puede usar el conector de Yammer en el flujo. Puede utilizar el nuevo mensaje recuperado desde el desencadenador de Yammer ("Nuevo mensaje") en otras acciones en el flujo. Configure las propiedades de entrada del desencadenador de Yammer de la siguiente manera:

	- **Id. de grupo**: el Id. del grupo desde el que se debe recuperar el nuevo mensaje. Si no se proporciona el Id. de grupo, el mensaje se recuperarán de la fuente siguiente. El Id. de grupo se puede recuperar desde la dirección URL de grupo en Yammer.
		Ejemplo: El Id. de grupo en la siguiente dirección URL es "5453203".
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	De forma similar, puede utilizar la acción de Yammer en el flujo para publicar un mensaje seleccionando la acción "Publicar mensaje". Configure las propiedades de entrada de la acción "Publicar mensaje" de la siguiente manera:
	- **Texto del mensaje** - Contenido de texto del mensaje que se va a publicar
	- **Id. de grupo** - Especifique el Id. del grupo desde el que se debe recuperar el nuevo mensaje. Si no se proporciona el Id. de grupo, el mensaje se enviará a toda la fuente de la compañía. El Id. de grupo se puede recuperar desde la dirección URL de grupo en Yammer. 
	Ejemplo: El Id. de grupo en la siguiente dirección URL es "5453203".
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Usuarios de etiqueta** - Matriz de nombres de red de usuario que tiene que etiquetarse en el mensaje. 
	- 	**Datos adjuntos**: datos adjuntos que se van a enviar junto con la publicación. Contiene los siguientes campos:
		- Nombre de archivo
		- Contenido
		- Codificación de transferencia de contenido
		- Tipo de contenido

	![][10]
	![][11]




<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!--HONumber=52-->